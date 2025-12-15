Filename pattern: src/modules/edit/client-information/reports/tests/ClientReportAutoCompleteInputBox.test.tsx

 ❯ src/modules/edit/client-information/reports/tests/ClientReportAutoCompleteInputBox.test.tsx (6 tests | 4 failed) 313ms
   ✓ ClientReportAutoCompleteInputBox > renders the input field correctly 141ms
   × ClientReportAutoCompleteInputBox > fetches suggestions when user types after debounce delay 17ms       
     → mockFetchSuggestions.mockResolvedValue is not a function
   × ClientReportAutoCompleteInputBox > displays options formatted correctly 6ms
     → mockFetchSuggestions.mockResolvedValue is not a function
   × ClientReportAutoCompleteInputBox > handles wildcard selection correctly 1ms
     → mockFetchSuggestions.mockResolvedValue is not a function
   ✓ ClientReportAutoCompleteInputBox > handles input changes calling the parent setter 141ms
   × ClientReportAutoCompleteInputBox > handles selection of an option 1ms
     → mockFetchSuggestions.mockResolvedValue is not a function
                                                                                                            
⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯ Failed Tests 4 ⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯
                                                                                                            
 FAIL  src/modules/edit/client-information/reports/tests/ClientReportAutoCompleteInputBox.test.tsx > ClientReportAutoCompleteInputBox > fetches suggestions when user types after debounce delay                        
TypeError: mockFetchSuggestions.mockResolvedValue is not a function
 ❯ src/modules/edit/client-information/reports/tests/ClientReportAutoCompleteInputBox.test.tsx:49:26        
     47|       { reportId: 102, name: 'Test Report B', fileExt: 'XLS' },
     48|     ];
     49|     mockFetchSuggestions.mockResolvedValue(mockData);
       |                          ^
     50|
     51|     render(

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯[1/4]⎯

 FAIL  src/modules/edit/client-information/reports/tests/ClientReportAutoCompleteInputBox.test.tsx > ClientReportAutoCompleteInputBox > displays options formatted correctly
TypeError: mockFetchSuggestions.mockResolvedValue is not a function
 ❯ src/modules/edit/client-information/reports/tests/ClientReportAutoCompleteInputBox.test.tsx:82:26        
     80|       { reportId: 101, name: 'ReportA', fileExt: 'PDF' },
     81|     ];
     82|     mockFetchSuggestions.mockResolvedValue(mockData);
       |                          ^
     83|
     84|     render(

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯[2/4]⎯

 FAIL  src/modules/edit/client-information/reports/tests/ClientReportAutoCompleteInputBox.test.tsx > ClientReportAutoCompleteInputBox > handles wildcard selection correctly
TypeError: mockFetchSuggestions.mockResolvedValue is not a function
 ❯ src/modules/edit/client-information/reports/tests/ClientReportAutoCompleteInputBox.test.tsx:111:26       
    109|   it('handles wildcard selection correctly', async () => {
    110|     const mockData = [{ reportId: 1, name: 'WildcardMatch' }];
    111|     mockFetchSuggestions.mockResolvedValue(mockData);
       |                          ^
    112|
    113|     render(

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯[3/4]⎯

 FAIL  src/modules/edit/client-information/reports/tests/ClientReportAutoCompleteInputBox.test.tsx > ClientReportAutoCompleteInputBox > handles selection of an option
TypeError: mockFetchSuggestions.mockResolvedValue is not a function
 ❯ src/modules/edit/client-information/reports/tests/ClientReportAutoCompleteInputBox.test.tsx:153:26       
    151|       { reportId: 999, name: 'SelectedReport', fileExt: 'CSV' },
    152|     ];
    153|     mockFetchSuggestions.mockResolvedValue(mockData);
       |                          ^
    154|
    155|     render(

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯[4/4]⎯


 Test Files  1 failed (1)
      Tests  4 failed | 2 passed (6)
   Start at  23:08:28
   Duration  5.93s

 FAIL  Tests failed. Watching for file changes...
       press h to show help, press q to quit
