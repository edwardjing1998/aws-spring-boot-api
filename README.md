// PART 1: FIX - Verify API Call (Debounce Logic)
  it('triggers the API call after the debounce period', async () => {
    // 1. Setup the mock return value
    mockFetchSuggestions.mockResolvedValue({ data: [] });

    render(
      <ClientReportAutoCompleteInputBox
        inputValue="test"
        setInputValue={mockSetInputValue}
        isWildcardMode={false}
      />
    );

    // 2. Advance time by 400ms (more than the 300ms delay)
    // We wrap this in act() so React processes the state updates
    act(() => {
      vi.advanceTimersByTime(400);
    });

    // 3. Assert immediately (No waitFor needed because time is frozen)
    expect(mockFetchSuggestions).toHaveBeenCalledWith('test');
  });
