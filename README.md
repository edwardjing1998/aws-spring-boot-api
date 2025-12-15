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

    // 1. Trigger the search logic
    act(() => {
      vi.advanceTimersByTime(400);
    });

    // 2. CRITICAL STEP: Manually open the dropdown
    // MUI Autocomplete needs a click/focus to show the list
    const input = screen.getByPlaceholderText('Search Report');
    fireEvent.mouseDown(input); 

    // 3. Now wait for the text to appear
    await waitFor(() => {
      expect(screen.getByText('123 :::: ReportA :::: pdf')).toBeDefined();
    });
  });
