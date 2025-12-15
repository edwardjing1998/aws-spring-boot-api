import { ClientReportItem, ClientReportRow } from './EditClientReport.types';

/**
 * Fetches client reports from the API and normalizes them into ClientReportRow format.
 */
export const fetchEditClientReport = async (
  clientId: string,
  page: number,
  pageSize: number
): Promise<ClientReportRow[]> => {
  const url = `http://localhost:8089/client-sysprin-reader/api/client/report-option/${encodeURIComponent(clientId)}?page=${page}&size=${pageSize}`;

  const resp = await fetch(url, {
    method: 'GET',
    headers: { accept: '*/*' },
  });

  if (!resp.ok) {
    throw new Error('Failed to fetch client reports');
  }

  const options: ClientReportItem[] = await resp.json();

  // Normalize incoming data to table row format
  const nextRows: ClientReportRow[] = Array.isArray(options)
    ? options.map((option) => ({
        reportName: option?.reportDetails?.queryName || '',
        reportId: option?.reportDetails?.reportId ?? option?.reportId ?? null,
        receive: option?.receiveFlag ? '1' : '2',
        destination: option?.outputTypeCd !== undefined ? String(option.outputTypeCd) : '0',
        fileText: option?.fileTypeCd !== undefined ? String(option.fileTypeCd) : '0',
        email: option?.emailFlag === 1 ? '1' : option?.emailFlag === 2 ? '2' : '0',
        password: option?.reportPasswordTx || '',
        emailBodyTx: option?.emailBodyTx || '',
        fileExt: option?.fileExt ?? option?.reportDetails?.fileExt ?? '',
      }))
    : [];

  return nextRows;
};

export const fetchClientReportSuggestions = async (keyword: string): Promise<any> => {
  const encoded = encodeURIComponent(keyword.trim());
  const endpoint = `http://localhost:8089/search-integration/api/report-autocomplete?keyword=${encoded}`;

  const res = await fetch(endpoint);
  
  if (!res.ok) {
    throw new Error(`Request failed: ${res.status} ${res.statusText}`);
  }
  
  return res.json();
};

/**
 * Fetches the paginated report options for a specific client.
 */
export const fetchPreviewClientReports = async (
  clientId: string,
  page: number,
  pageSize: number
): Promise<ClientReportItem[]> => {
  try {
    const url = `http://localhost:8089/client-sysprin-reader/api/client/report-option/${encodeURIComponent(clientId)}?page=${page}&size=${pageSize}`;

    const resp = await fetch(url, {
      method: 'GET',
      headers: { accept: '*/*' },
    });

    if (resp.ok) {
      const json: ClientReportItem[] = await resp.json();
      return json;
    } else {
      console.error("Failed to fetch reports");
      return [];
    }
  } catch (error) {
    console.error("Error fetching reports:", error);
    return [];
  }
};

