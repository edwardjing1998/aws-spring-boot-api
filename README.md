// PART 3: FIX - Verify Dropdown Options Format
  it('renders options with the correct format', async () => {
    const mockData = [{ reportId: 123, name: 'ReportA', fileExt: 'pdf' }];
    mockFetchSuggestions.mockResolvedValue({ data: mockData });

    render(
      <ClientReportAutoCompleteInputBox
        inputValue="ReportA"
        setInputValue={mockSetInputValue}
        isWildcardMode={false}
      />
    );

    const input = screen.getByPlaceholderText('Search Report');

    // 1. Focus the input so the dropdown is allowed to open
    fireEvent.focus(input);
    fireEvent.mouseDown(input);

    // 2. Advance the timer to trigger the API call
    act(() => {
      vi.advanceTimersByTime(400);
    });

    // 3. CRITICAL: Use `findByText` instead of `waitFor` + `getByText`
    // findByText is specifically designed to wait for async UI updates 
    // and works better with Promise resolution.
    const option = await screen.findByText('123 :::: ReportA :::: pdf');
    expect(option).toBeDefined();
  });
