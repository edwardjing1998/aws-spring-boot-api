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








   Filename pattern: src/modules/edit/client-information/reports/tests/ClientReportAutoCompleteInputBox.test.tsx

 ❯ src/modules/edit/client-information/reports/tests/ClientReportAutoCompleteInputBox.test.tsx (4 tests | 1 failed) 5640ms
   ✓ ClientReportAutoCompleteInputBox > renders the input field correctly 263ms
   ✓ ClientReportAutoCompleteInputBox > handles input changes calling the parent setter 288ms
   ✓ ClientReportAutoCompleteInputBox > triggers the API call after the debounce period 46ms
   × ClientReportAutoCompleteInputBox > renders options with the correct format 5039ms
     → Test timed out in 5000ms.
If this is a long-running test, pass a timeout value as the last argument or configure it globally with "testTimeout".
                                                                                                            
⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯ Failed Tests 1 ⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯
                                                                                                            
 FAIL  src/modules/edit/client-information/reports/tests/ClientReportAutoCompleteInputBox.test.tsx > ClientReportAutoCompleteInputBox > renders options with the correct format                                         
Error: Test timed out in 5000ms.
If this is a long-running test, pass a timeout value as the last argument or configure it globally with "testTimeout".
 ❯ src/modules/edit/client-information/reports/tests/ClientReportAutoCompleteInputBox.test.tsx:84:3
     82|
     83|   // PART 3: NEW - Verify Dropdown Options Format
     84|   it('renders options with the correct format', async () => {
       |   ^
     85|     const mockData = [{ reportId: 123, name: 'ReportA', fileExt: 'pdf' }];
     86|     mockFetchSuggestions.mockResolvedValue({ data: mockData });

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯[1/1]⎯


 Test Files  1 failed (1)
      Tests  1 failed | 3 passed (4)
   Start at  08:07:30
   Duration  14.46s

 FAIL  Tests failed. Watching for file changes...
       press h to show help, press q to quit

