// PART 3: NEW - Verify Dropdown Options Format
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

    // Trigger the search
    act(() => {
      vi.advanceTimersByTime(400);
    });

    // The component uses MUI Autocomplete. 
    // We usually need to click the input or wait for the list to appear.
    // However, since `open` isn't controlled, we just wait for the text to appear in the DOM.
    await waitFor(() => {
      // This specific string format must match your code: `${id} :::: ${name} :::: ${ext}`
      expect(screen.getByText('123 :::: ReportA :::: pdf')).toBeDefined();
    });
  });
