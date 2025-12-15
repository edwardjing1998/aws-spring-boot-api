Filename pattern: src/modules/edit/client-information/reports/tests/ClientReportAutoCompleteInputBox.test.tsx

 ❯ src/modules/edit/client-information/reports/tests/ClientReportAutoCompleteInputBox.test.tsx (4 tests | 1 failed) 5319ms
   ✓ ClientReportAutoCompleteInputBox > renders the input field correctly 131ms
   ✓ ClientReportAutoCompleteInputBox > handles input changes calling the parent setter 140ms
   ✓ ClientReportAutoCompleteInputBox > triggers the API call after the debounce period 25ms
   × ClientReportAutoCompleteInputBox > renders options with the correct format 5020ms
     → Test timed out in 5000ms.
If this is a long-running test, pass a timeout value as the last argument or configure it globally with "testTimeout".
                                                                                                            
⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯ Failed Tests 1 ⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯
                                                                                                            
 FAIL  src/modules/edit/client-information/reports/tests/ClientReportAutoCompleteInputBox.test.tsx > ClientReportAutoCompleteInputBox > renders options with the correct format                                         
Error: Test timed out in 5000ms.
If this is a long-running test, pass a timeout value as the last argument or configure it globally with "testTimeout".
 ❯ src/modules/edit/client-information/reports/tests/ClientReportAutoCompleteInputBox.test.tsx:84:5
     82| 
     83|   // PART 3: FIX - Verify Dropdown Options Format
     84|     it('renders options with the correct format', async () => {
       |     ^
     85|       const mockData = [{ reportId: 123, name: 'ReportA', fileExt: 'pdf' }];
     86|       mockFetchSuggestions.mockResolvedValue({ data: mockData });

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯[1/1]⎯


 Test Files  1 failed (1)
      Tests  1 failed | 3 passed (4)
   Start at  08:26:28
   Duration  10.13s

 FAIL  Tests failed. Watching for file changes...
       press h to show help, press q to quit
