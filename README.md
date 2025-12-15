ClientReportWindow.test.tsx


// @vitest-environment happy-dom
import React from 'react';
import { render, screen, fireEvent, waitFor, cleanup } from '@testing-library/react';
import { vi, describe, it, expect, beforeEach, afterEach } from 'vitest';
import ClientReportWindow, { ClientReportWindowProps } from './ClientReportWindow';
import { ClientReportRow } from './EditClientReport.types';

// --- Mocks ---

// 1. Mock the Autocomplete component
// We replace the complex MUI autocomplete with a simple input that calls setInputValue.
// This allows us to test if the PARENT correctly processes the string typed into it.
vi.mock('./ClientReportAutoCompleteInputBox', () => ({
  default: ({ inputValue, setInputValue }: any) => (
    <input
      data-testid="mock-autocomplete"
      value={inputValue}
      onChange={(e) => setInputValue(e.target.value)}
    />
  ),
}));

// 2. Mock Global Fetch
const mockFetch = vi.fn();
global.fetch = mockFetch;

describe('ClientReportWindow', () => {
  const mockOnClose = vi.fn();
  const mockOnSave = vi.fn();
  const mockOnDelete = vi.fn();
  const clientId = 'CL-123';

  const defaultRow: ClientReportRow = {
    reportId: 101,
    reportName: 'Existing Report',
    receive: '1',
    destination: '1',
    fileText: '1',
    email: '0',
    password: 'pass',
    emailBodyTx: 'body',
    fileExt: 'pdf',
  };

  const defaultProps: ClientReportWindowProps = {
    open: true,
    clientId,
    row: defaultRow,
    onClose: mockOnClose,
    onSave: mockOnSave,
    onDelete: mockOnDelete,
    mode: 'detail',
  };

  beforeEach(() => {
    vi.clearAllMocks();
    mockFetch.mockResolvedValue({
      ok: true,
      json: async () => ({ success: true }),
      text: async () => 'OK',
    });
  });

  afterEach(() => {
    cleanup();
  });

  // --- Test 1: Rendering Modes ---

  it('renders correctly in DETAIL mode (Read Only)', () => {
    render(<ClientReportWindow {...defaultProps} mode="detail" />);

    expect(screen.getByText('Report Details')).toBeDefined();
    // Inputs should NOT be present (rendered as text)
    expect(screen.queryByRole('textbox')).toBeNull(); 
    // Values should be visible as text
    expect(screen.getByText('Existing Report')).toBeDefined();
    expect(screen.getByText('Yes')).toBeDefined(); // Mapped from '1'
    expect(screen.getByText('File')).toBeDefined(); // Mapped from '1'
  });

  it('renders correctly in EDIT mode', () => {
    render(<ClientReportWindow {...defaultProps} mode="edit" />);

    expect(screen.getByText('Edit Client Report')).toBeDefined();
    // Inputs should be present
    expect(screen.getByDisplayValue('Existing Report')).toBeDefined();
    expect(screen.getByText('Save')).toBeDefined();
  });

  it('renders correctly in NEW mode', () => {
    render(<ClientReportWindow {...defaultProps} mode="new" row={null} />);

    expect(screen.getByText('New Client Report')).toBeDefined();
    // Should see our mocked autocomplete
    expect(screen.getByTestId('mock-autocomplete')).toBeDefined();
    // Create button disabled initially
    expect(screen.getByText('Create')).toBeDisabled();
  });

  it('renders correctly in DELETE mode', () => {
    render(<ClientReportWindow {...defaultProps} mode="delete" />);

    expect(screen.getByText('Delete Client Report')).toBeDefined();
    expect(screen.getByText(/This action cannot be undone/i)).toBeDefined();
    expect(screen.getByText('Delete')).toBeDefined();
  });

  // --- Test 2: "New" Mode Logic (Extraction & Submit) ---

  it('extracts ID, Name, and Extension from Autocomplete string in NEW mode', async () => {
    render(<ClientReportWindow {...defaultProps} mode="new" row={null} />);

    const input = screen.getByTestId('mock-autocomplete');

    // Simulate selecting a report formatted as: "ID :::: NAME :::: EXT"
    // The component's useEffect logic should parse this.
    fireEvent.change(input, { target: { value: '999 :::: New Report :::: csv' } });

    // Verify "Create" is now enabled (because ID and Name are populated)
    const createBtn = screen.getByText('Create');
    expect(createBtn).not.toBeDisabled();

    // Click Create
    fireEvent.click(createBtn);

    // Verify API Payload
    await waitFor(() => {
      expect(mockFetch).toHaveBeenCalledWith(
        expect.stringContaining('/Initiate'),
        expect.objectContaining({
          method: 'POST',
          body: expect.stringContaining('"reportId":999'),
        })
      );
    });

    // Verify other extracted fields in payload
    const callArgs = mockFetch.mock.calls[0]; // [url, options]
    const body = JSON.parse(callArgs[1].body);

    expect(body.fileExt).toBe('csv'); // Extracted correctly?
    expect(mockOnSave).toHaveBeenCalled(); // Parent callback fired?
  });

  // --- Test 3: "Edit" Mode Logic ---

  it('updates form values and calls API on Save in EDIT mode', async () => {
    render(<ClientReportWindow {...defaultProps} mode="edit" />);

    // Change "Receive" dropdown (Select component)
    // Note: In MUI Select testing, we often target the hidden input or use mouseDown on the button.
    // However, since we used a simple `handleChange` wrapper in the component, 
    // we can simulate changing the value if we can find the input.
    // A simpler way for this specific component structure:
    
    // Find the input for "Password" and change it
    const passInput = screen.getByDisplayValue('pass');
    fireEvent.change(passInput, { target: { value: 'newpassword' } });

    // Click Save
    fireEvent.click(screen.getByText('Save'));

    await waitFor(() => {
      expect(mockFetch).toHaveBeenCalledWith(
        expect.stringContaining('/update'),
        expect.objectContaining({
          method: 'POST',
          body: expect.stringContaining('"reportPasswordTx":"newpassword"'),
        })
      );
    });
    
    expect(screen.getByText('Report updated successfully.')).toBeDefined();
  });

  // --- Test 4: "Delete" Mode Logic ---

  it('calls delete API and notifies parent', async () => {
    render(<ClientReportWindow {...defaultProps} mode="delete" />);

    fireEvent.click(screen.getByText('Delete'));

    await waitFor(() => {
      expect(mockFetch).toHaveBeenCalledWith(
        // Verify URL contains IDs
        expect.stringContaining(`/${encodeURIComponent(clientId)}/${defaultRow.reportId}`),
        expect.objectContaining({ method: 'DELETE' })
      );
    });

    expect(mockOnDelete).toHaveBeenCalled();
  });

  // --- Test 5: Error Handling ---

  it('displays error message if API fails', async () => {
    mockFetch.mockResolvedValueOnce({
      ok: false,
      text: async () => 'Server Error 500',
    });

    render(<ClientReportWindow {...defaultProps} mode="edit" />);
    
    fireEvent.click(screen.getByText('Save'));

    await waitFor(() => {
      expect(screen.getByText('Submit failed: Server Error 500')).toBeDefined();
    });
  });

  // --- Test 6: Closing ---

  it('calls onClose when Close button is clicked', () => {
    render(<ClientReportWindow {...defaultProps} />);
    fireEvent.click(screen.getByText('Close'));
    expect(mockOnClose).toHaveBeenCalled();
  });
});
