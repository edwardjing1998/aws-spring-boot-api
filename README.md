npx stryker run


ClientReport.service.test.tsx


import { describe, it, expect, vi, beforeEach, afterEach, Mock } from 'vitest';
import { 
  fetchEditClientReport, 
  fetchClientReportSuggestions, 
  fetchPreviewClientReports 
} from './ClientReportIntegrationService'; // Adjust path if needed

// Mock global fetch
const mockFetch = vi.fn();
global.fetch = mockFetch;

describe('ClientReportIntegrationService', () => {

  beforeEach(() => {
    vi.clearAllMocks();
  });

  afterEach(() => {
    vi.restoreAllMocks();
  });

  // --- Test Suite 1: fetchEditClientReport ---
  describe('fetchEditClientReport', () => {
    const clientId = '12345';
    const page = 1;
    const pageSize = 10;
    const expectedUrl = `http://localhost:8089/client-sysprin-reader/api/client/report-option/12345?page=1&size=10`;

    it('successfully fetches and maps data to table row format', async () => {
      // Mock raw API response
      const mockApiResponse = [
        {
          reportDetails: { queryName: 'Report A', reportId: 101, fileExt: 'pdf' },
          receiveFlag: true,
          outputTypeCd: 5,
          fileTypeCd: 3,
          emailFlag: 1, // Should map to '1'
          reportPasswordTx: 'secret',
          emailBodyTx: 'Hello',
          fileExt: null // Should fallback to reportDetails.fileExt
        }
      ];

      mockFetch.mockResolvedValue({
        ok: true,
        json: async () => mockApiResponse,
      });

      const result = await fetchEditClientReport(clientId, page, pageSize);

      // 1. Verify URL construction
      expect(mockFetch).toHaveBeenCalledWith(expectedUrl, expect.objectContaining({ method: 'GET' }));

      // 2. Verify Mapping Logic
      expect(result).toHaveLength(1);
      expect(result[0]).toEqual({
        reportName: 'Report A',
        reportId: 101,
        receive: '1',      // receiveFlag true -> '1'
        destination: '5',  // outputTypeCd 5 -> '5'
        fileText: '3',     // fileTypeCd 3 -> '3'
        email: '1',        // emailFlag 1 -> '1'
        password: 'secret',
        emailBodyTx: 'Hello',
        fileExt: 'pdf'     // Fallback logic check
      });
    });

    it('handles alternative mapping scenarios (false flags, missing data)', async () => {
      const mockApiResponse = [
        {
          // Missing reportDetails entirely
          reportId: 202, 
          receiveFlag: false, // Should map to '2'
          // Missing outputTypeCd -> '0'
          // Missing fileTypeCd -> '0'
          emailFlag: 2,       // Should map to '2'
          fileExt: 'csv'      // Direct fileExt
        }
      ];

      mockFetch.mockResolvedValue({
        ok: true,
        json: async () => mockApiResponse,
      });

      const result = await fetchEditClientReport(clientId, page, pageSize);

      expect(result[0]).toEqual(expect.objectContaining({
        reportName: '',    // Fallback
        reportId: 202,     // Fallback to top level reportId
        receive: '2',      // receiveFlag false -> '2'
        destination: '0',  // undefined -> '0'
        fileText: '0',     // undefined -> '0'
        email: '2',        // emailFlag 2 -> '2'
        fileExt: 'csv'
      }));
    });

    it('throws an error if the response is not ok', async () => {
      mockFetch.mockResolvedValue({
        ok: false,
        status: 500,
      });

      await expect(fetchEditClientReport(clientId, page, pageSize))
        .rejects.toThrow('Failed to fetch client reports');
    });
  });

  // --- Test Suite 2: fetchClientReportSuggestions ---
  describe('fetchClientReportSuggestions', () => {
    it('successfully fetches suggestions with encoded keyword', async () => {
      const keyword = 'test data';
      const expectedEndpoint = `http://localhost:8089/search-integration/api/report-autocomplete?keyword=test%20data`;
      const mockData = [{ id: 1, name: 'Suggestion' }];

      mockFetch.mockResolvedValue({
        ok: true,
        json: async () => mockData,
      });

      const result = await fetchClientReportSuggestions(keyword);

      expect(mockFetch).toHaveBeenCalledWith(expectedEndpoint);
      expect(result).toEqual(mockData);
    });

    it('throws an error when response fails', async () => {
      mockFetch.mockResolvedValue({
        ok: false,
        status: 404,
        statusText: 'Not Found',
      });

      await expect(fetchClientReportSuggestions('abc'))
        .rejects.toThrow('Request failed: 404 Not Found');
    });
  });

  // --- Test Suite 3: fetchPreviewClientReports ---
  describe('fetchPreviewClientReports', () => {
    const clientId = '999';
    
    it('returns data on success', async () => {
      const mockData = [{ reportId: 1 }];
      mockFetch.mockResolvedValue({
        ok: true,
        json: async () => mockData,
      });

      const result = await fetchPreviewClientReports(clientId, 1, 10);
      expect(result).toEqual(mockData);
    });

    it('returns empty array and logs error on API failure (ok: false)', async () => {
      // Spy on console.error to keep test output clean and verify call
      const consoleSpy = vi.spyOn(console, 'error').mockImplementation(() => {});

      mockFetch.mockResolvedValue({
        ok: false,
      });

      const result = await fetchPreviewClientReports(clientId, 1, 10);

      expect(result).toEqual([]);
      expect(consoleSpy).toHaveBeenCalledWith("Failed to fetch reports");
    });

    it('returns empty array and logs error on Network Exception', async () => {
      const consoleSpy = vi.spyOn(console, 'error').mockImplementation(() => {});

      mockFetch.mockRejectedValue(new Error('Network Error'));

      const result = await fetchPreviewClientReports(clientId, 1, 10);

      expect(result).toEqual([]);
      expect(consoleSpy).toHaveBeenCalledWith("Error fetching reports:", expect.any(Error));
    });
  });
});


