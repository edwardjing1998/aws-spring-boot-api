// @vitest-environment happy-dom
import React from 'react';
import { render, screen, fireEvent, waitFor, act, cleanup } from '@testing-library/react';
import { vi, describe, it, expect, beforeEach, afterEach, Mock } from 'vitest';
import ClientReportAutoCompleteInputBox from './ClientReportAutoCompleteInputBox';

// Use vi.hoisted to create the mock function before modules are imported
const { fetchClientReportSuggestions: mockFetchSuggestions } = vi.hoisted(() => ({
  fetchClientReportSuggestions: vi.fn(),
}));

// Mock the service module using the hoisted mock
vi.mock('../views/sys-prin-configuration/utils/ClientReportIntegrationService', () => ({
  fetchClientReportSuggestions: mockFetchSuggestions,
}));

describe('ClientReportAutoCompleteInputBox', () => {
  const mockSetInputValue = vi.fn();
  const mockOnClientsFetched = vi.fn();
  const mockSetIsWildcardMode = vi.fn();

  beforeEach(() => {
    vi.clearAllMocks();
    vi.useFakeTimers();
  });

  afterEach(() => {
    cleanup();
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

    expect(screen.getByPlaceholderText('Search Report')).toBeDefined();
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
});
