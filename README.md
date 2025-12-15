// ClientReportAutoCompleteInputBox.test.tsx

import React from 'react';
import { render, screen, fireEvent, waitFor, act } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import ClientReportAutoCompleteInputBox from './ClientReportAutoCompleteInputBox';
import * as Service from '../views/sys-prin-configuration/utils/ClientReportIntegrationService';

// Mock the service
jest.mock('../views/sys-prin-configuration/utils/ClientReportIntegrationService');

const mockFetchSuggestions = Service.fetchClientReportSuggestions as jest.Mock;

describe('ClientReportAutoCompleteInputBox', () => {
  const mockSetInputValue = jest.fn();
  const mockOnClientsFetched = jest.fn();
  const mockSetIsWildcardMode = jest.fn();

  beforeEach(() => {
    jest.clearAllMocks();
    jest.useFakeTimers();
  });

  afterEach(() => {
    jest.useRealTimers();
  });

  it('renders the input field correctly', () => {
    render(
      <ClientReportAutoCompleteInputBox
        inputValue=""
        setInputValue={mockSetInputValue}
        isWildcardMode={false}
      />
    );

    expect(screen.getByPlaceholderText('Search Report')).toBeInTheDocument();
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

    const input = screen.getByPlaceholderText('Search Report');

    // Simulate typing
    // Note: In controlled components with debounce in useEffect based on prop, 
    // we need to simulate the parent updating the prop or just test the effect trigger.
    // However, the component relies on `inputValue` prop for the useEffect dependency.
    // Tests usually need to re-render with new props to trigger the effect if logic is in parent.
    // But here we can simulate the event triggering the parent's setter.
    
    // For this specific test setup where we want to test the internal useEffect logic,
    // we should render with the value that triggers the effect.
    
    const { rerender } = render(
      <ClientReportAutoCompleteInputBox
        inputValue="Test"
        setInputValue={mockSetInputValue}
        isWildcardMode={false}
      />
    );

    // Fast-forward debounce time
    act(() => {
      jest.advanceTimersByTime(300);
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
      jest.advanceTimersByTime(300);
    });

    // Need to trigger the autocomplete dropdown opening
    const input = screen.getByPlaceholderText('Search Report');
    fireEvent.click(input); 
    // Or normally typing triggers it, but we start with value here.
    // Let's ensure focus triggers state check if applicable, 
    // or just rely on the fact that Autocomplete shows options when they arrive if focused.
    fireEvent.focus(input);
    fireEvent.change(input, { target: { value: 'Report' } });

    await waitFor(() => {
      // Material UI Autocomplete often renders options in a portal
      // The format logic is: `${opt.reportId} :::: ${opt.name}  :::: ${opt.fileExt}`
      const optionText = "101 :::: ReportA  :::: PDF";
      expect(screen.getByText(optionText)).toBeInTheDocument();
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
      jest.advanceTimersByTime(300);
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

    // Setup component with initial search value so options load
    render(
      <ClientReportAutoCompleteInputBox
        inputValue="Selected"
        setInputValue={mockSetInputValue}
        isWildcardMode={false}
      />
    );

    // Trigger fetch
    act(() => {
      jest.advanceTimersByTime(300);
    });

    await waitFor(() => {
        expect(screen.queryByText(/999/)).toBeInTheDocument();
    });

    // Click the option
    const option = screen.getByText(/999 :::: SelectedReport/);
    fireEvent.click(option);

    // Check if setInputValue was called with the formatted string upon selection
    // The Autocomplete default behavior on selection is to call onInputChange 
    // with the option label/value.
    expect(mockSetInputValue).toHaveBeenCalledWith(
        expect.stringContaining("999 :::: SelectedReport")
    );
  });
});
