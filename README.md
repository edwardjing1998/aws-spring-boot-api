// PART 3: FINAL FIX - Use Real Timers for UI Integration
  it('renders options with the correct format', async () => {
    // 1. SWITCH TO REAL TIMERS
    // This allows Promises and MUI animations to run naturally without freezing.
    vi.useRealTimers();

    const mockData = [{ reportId: 123, name: 'ReportA', fileExt: 'pdf' }];
    mockFetchSuggestions.mockResolvedValue({ data: mockData });

    // 2. Render with the input value already set to trigger the effect
    render(
      <ClientReportAutoCompleteInputBox
        inputValue="ReportA"
        setInputValue={mockSetInputValue}
        isWildcardMode={false}
      />
    );

    const input = screen.getByPlaceholderText('Search Report');

    // 3. Focus and MouseDown to ensure MUI opens the dropdown
    fireEvent.focus(input);
    fireEvent.mouseDown(input);

    // 4. Wait for the text to appear (Standard Async Wait)
    // We don't need 'act' or 'advanceTimers' because we are using real time.
    // 'findByText' will poll until the 300ms debounce passes + API returns + Render happens.
    const option = await screen.findByText('123 :::: ReportA :::: pdf', {}, { timeout: 2000 });
    
    expect(option).toBeDefined();
  });
