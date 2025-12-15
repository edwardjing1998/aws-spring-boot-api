// Add this inside your describe block
it('triggers the API call after the debounce period', async () => {
  mockFetchSuggestions.mockResolvedValue({ data: [] }); // Setup the mock return

  render(
    <ClientReportAutoCompleteInputBox
      inputValue="test"
      setInputValue={mockSetInputValue}
      isWildcardMode={false}
    />
  );

  // Fast-forward timers to trigger the debounce
  act(() => {
    vi.advanceTimersByTime(300);
  });

  await waitFor(() => {
    expect(mockFetchSuggestions).toHaveBeenCalledWith('test');
  });
});
