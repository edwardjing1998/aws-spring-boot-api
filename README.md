// @vitest-environment happy-dom
import React from 'react';
import { render, screen, fireEvent, waitFor, act } from '@testing-library/react';
import { vi, describe, it, expect, beforeEach, afterEach, Mock } from 'vitest';
import ClientReportAutoCompleteInputBox from './ClientReportAutoCompleteInputBox';
import * as Service from '../views/sys-prin-configuration/utils/ClientReportIntegrationService';

// Mock the service with a factory to ensure it's a spy function
vi.mock('../views/sys-prin-configuration/utils/ClientReportIntegrationService', () => ({
  fetchClientReportSuggestions: vi.fn(),
}));

const mockFetchSuggestions = Service.fetchClientReportSuggestions as Mock;

describe('ClientReportAutoCompleteInputBox', () => {
  const mockSetInputValue = vi.fn();
  const mockOnClientsFetched = vi.fn();
  const mockSetIsWildcardMode = vi.fn();

  beforeEach(() => {
    vi.clearAllMocks();
    vi.useFakeTimers();
  });

  afterEach(() => {
    vi.useRealTimers();
  });

  it('renders the input field correctly', () => {
    render(
      <ClientReportAutoCompleteInputBox
        inputValue=""
        setInputValue={mockSetInputValue}
        isWildcardMode={false}
      />
    );

    // getBy... throws if not found, so existence is implicitly verified.
    // Adding .toBeDefined() makes the intent explicit.
    expect(screen.getByPlaceholderText('Search Report')).toBeDefined();
  });

  it('fetches suggestions when user types after debounce delay', async () => {
    const mockData = [
      { reportId: 101, name: 'Test Report A', fileExt: 'PDF' },
      { reportId: 102, name: 'Test Report B', fileExt: 'XLS' },
    ];
    mockFetchSuggestions.mockResolvedValue(mockData);

    render(
      <ClientReportAutoCompleteInputBox
        inputValue=""
        setInputValue={mockSetInputValue}
        isWildcardMode={false}
      />
    );

    // Simulate typing logic (rendering with new value)
    const { rerender } = render(
      <ClientReportAutoCompleteInputBox
        inputValue="Test"
        setInputValue={mockSetInputValue}
        isWildcardMode={false}
      />
    );

    // Fast-forward debounce time
    act(() => {
      vi.advanceTimersByTime(300);
    });

    await waitFor(() => {
      expect(mockFetchSuggestions).toHaveBeenCalledWith('Test');
    });
  });

  it('displays options formatted correctly', async () => {
    const mockData = [
      { reportId: 101, name: 'ReportA', fileExt: 'PDF' },
    ];
    mockFetchSuggestions.mockResolvedValue(mockData);

    render(
      <ClientReportAutoCompleteInputBox
        inputValue="Report"
        setInputValue={mockSetInputValue}
        isWildcardMode={false}
      />
    );

    act(() => {
      vi.advanceTimersByTime(300);
    });

    const input = screen.getByPlaceholderText('Search Report');
    fireEvent.click(input); 
    fireEvent.focus(input);
    fireEvent.change(input, { target: { value: 'Report' } });

    await waitFor(() => {
      const optionText = "101 :::: ReportA  :::: PDF";
      expect(screen.getByText(optionText)).toBeDefined();
    });
  });

  it('handles wildcard selection correctly', async () => {
    const mockData = [{ reportId: 1, name: 'WildcardMatch' }];
    mockFetchSuggestions.mockResolvedValue(mockData);

    render(
      <ClientReportAutoCompleteInputBox
        inputValue="Test*"
        setInputValue={mockSetInputValue}
        onClientsFetched={mockOnClientsFetched}
        setIsWildcardMode={mockSetIsWildcardMode}
        isWildcardMode={false}
      />
    );

    act(() => {
      vi.advanceTimersByTime(300);
    });

    await waitFor(() => {
      expect(mockFetchSuggestions).toHaveBeenCalledWith('Test*');
      expect(mockSetIsWildcardMode).toHaveBeenCalledWith(true);
      expect(mockOnClientsFetched).toHaveBeenCalledWith(mockData);
    });
  });

  it('handles input changes calling the parent setter', async () => {
    render(
      <ClientReportAutoCompleteInputBox
        inputValue=""
        setInputValue={mockSetInputValue}
        isWildcardMode={false}
      />
    );

    const input = screen.getByPlaceholderText('Search Report');
    fireEvent.change(input, { target: { value: 'A' } });

    expect(mockSetInputValue).toHaveBeenCalledWith('A');
  });

  it('handles selection of an option', async () => {
    const mockData = [
      { reportId: 999, name: 'SelectedReport', fileExt: 'CSV' },
    ];
    mockFetchSuggestions.mockResolvedValue(mockData);

    render(
      <ClientReportAutoCompleteInputBox
        inputValue="Selected"
        setInputValue={mockSetInputValue}
        isWildcardMode={false}
      />
    );

    act(() => {
      vi.advanceTimersByTime(300);
    });

    await waitFor(() => {
        // queryByText returns null if not found, unlike getByText which throws
        expect(screen.queryByText(/999/)).not.toBeNull();
    });

    const option = screen.getByText(/999 :::: SelectedReport/);
    fireEvent.click(option);

    expect(mockSetInputValue).toHaveBeenCalledWith(
        expect.stringContaining("999 :::: SelectedReport")
    );
  });
});
