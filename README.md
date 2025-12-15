// PART 2: NEW - Verify Wildcard Logic
  it('sets wildcard mode and calls callback when input ends with *', async () => {
    // Mock data response (simulating what the service returns)
    const mockData = [{ reportId: 99, name: 'All', fileExt: 'csv' }];
    mockFetchSuggestions.mockResolvedValue({ data: mockData });

    render(
      <ClientReportAutoCompleteInputBox
        inputValue="test*"
        setInputValue={mockSetInputValue}
        // Pass the specific props we want to test
        setIsWildcardMode={mockSetIsWildcardMode}
        onClientsFetched={mockOnClientsFetched}
      />
    );

    // Fast-forward past the debounce
    act(() => {
      vi.advanceTimersByTime(400);
    });

    // Verify the Promise resolved and callbacks fired
    // We use a small waitFor here just to catch the Promise resolution microtask
    await waitFor(() => {
       expect(mockSetIsWildcardMode).toHaveBeenCalledWith(true);
       expect(mockOnClientsFetched).toHaveBeenCalledWith(mockData);
    });
  });
