// @vitest-environment happy-dom
import React from 'react';
import { render, screen, fireEvent, waitFor, cleanup } from '@testing-library/react';
import { vi, describe, it, expect, beforeEach, afterEach } from 'vitest';
import ClientReportWindow, { ClientReportWindowProps } from './ClientReportWindow';
import { ClientReportRow } from './EditClientReport.types';

// --- Mocks ---

// 1. Mock the Autocomplete component
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
    expect(screen.queryByRole('textbox')).toBeNull();
    expect(screen.getByText('Existing Report')).toBeDefined();
    expect(screen.getByText('Yes')).toBeDefined();
    expect(screen.getByText('File')).toBeDefined();
  });

  it('renders correctly in EDIT mode', () => {
    render(<ClientReportWindow {...defaultProps} mode="edit" />);

    expect(screen.getByText('Edit Client Report')).toBeDefined();
    // Use getByDisplayValue to verify input content
    expect(screen.getByDisplayValue('Existing Report')).toBeDefined();
    expect(screen.getByText('Save')).toBeDefined();
  });

  it('renders correctly in NEW mode', () => {
    render(<ClientReportWindow {...defaultProps} mode="new" row={null} />);

    expect(screen.getByText('New Client Report')).toBeDefined();
    expect(screen.getByTestId('mock-autocomplete')).toBeDefined();

    // FIX: Check the 'disabled' property directly instead of using toBeDisabled()
    const createBtn = screen.getByText('Create') as HTMLButtonElement;
    expect(createBtn.disabled).toBe(true);
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

    // Simulate input: "ID :::: NAME :::: EXT"
    fireEvent.change(input, { target: { value: '999 :::: New Report :::: csv' } });

    // FIX: Check that the button is NO LONGER disabled
    const createBtn = screen.getByText('Create') as HTMLButtonElement;
    expect(createBtn.disabled).toBe(false);

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

    const callArgs = mockFetch.mock.calls[0];
    const body = JSON.parse(callArgs[1].body as string);

    expect(body.fileExt).toBe('csv');
    expect(mockOnSave).toHaveBeenCalled();
  });

  // --- Test 3: "Edit" Mode Logic ---

  it('updates form values and calls API on Save in EDIT mode', async () => {
    render(<ClientReportWindow {...defaultProps} mode="edit" />);

    const passInput = screen.getByDisplayValue('pass');
    fireEvent.change(passInput, { target: { value: 'newpassword' } });

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
