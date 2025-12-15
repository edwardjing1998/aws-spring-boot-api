 Filename pattern: src/modules/edit/client-information/reports/tests/ClientReportAutoCompleteInputBox.test.tsx

 ❯ src/modules/edit/client-information/reports/tests/ClientReportAutoCompleteInputBox.test.tsx (6 tests | 4 failed) 242ms
   ✓ ClientReportAutoCompleteInputBox > renders the input field correctly 96ms
   × ClientReportAutoCompleteInputBox > fetches suggestions when user types after debounce delay 13ms       
     → mockFetchSuggestions.mockResolvedValue is not a function
   × ClientReportAutoCompleteInputBox > displays options formatted correctly 5ms
     → mockFetchSuggestions.mockResolvedValue is not a function
   × ClientReportAutoCompleteInputBox > handles wildcard selection correctly 1ms
     → mockFetchSuggestions.mockResolvedValue is not a function
   ✓ ClientReportAutoCompleteInputBox > handles input changes calling the parent setter 121ms
   × ClientReportAutoCompleteInputBox > handles selection of an option 2ms
     → mockFetchSuggestions.mockResolvedValue is not a function

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯ Failed Tests 4 ⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯

 FAIL  src/modules/edit/client-information/reports/tests/ClientReportAutoCompleteInputBox.test.tsx > ClientReportAutoCompleteInputBox > fetches suggestions when user types after debounce delay
TypeError: mockFetchSuggestions.mockResolvedValue is not a function
 ❯ src/modules/edit/client-information/reports/tests/ClientReportAutoCompleteInputBox.test.tsx:48:26        
     46|       { reportId: 102, name: 'Test Report B', fileExt: 'XLS' },
     47|     ];
     48|     mockFetchSuggestions.mockResolvedValue(mockData);
       |                          ^
     49|
     50|     render(

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯[1/4]⎯

 FAIL  src/modules/edit/client-information/reports/tests/ClientReportAutoCompleteInputBox.test.tsx > ClientReportAutoCompleteInputBox > displays options formatted correctly
TypeError: mockFetchSuggestions.mockResolvedValue is not a function
 ❯ src/modules/edit/client-information/reports/tests/ClientReportAutoCompleteInputBox.test.tsx:79:26        
     77|       { reportId: 101, name: 'ReportA', fileExt: 'PDF' },
     78|     ];
     79|     mockFetchSuggestions.mockResolvedValue(mockData);
       |                          ^
     80|
     81|     render(

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯[2/4]⎯

 FAIL  src/modules/edit/client-information/reports/tests/ClientReportAutoCompleteInputBox.test.tsx > ClientReportAutoCompleteInputBox > handles wildcard selection correctly
TypeError: mockFetchSuggestions.mockResolvedValue is not a function
 ❯ src/modules/edit/client-information/reports/tests/ClientReportAutoCompleteInputBox.test.tsx:106:26
    104|   it('handles wildcard selection correctly', async () => {
    105|     const mockData = [{ reportId: 1, name: 'WildcardMatch' }];
    106|     mockFetchSuggestions.mockResolvedValue(mockData);
       |                          ^
    107|
    108|     render(

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯[3/4]⎯

 FAIL  src/modules/edit/client-information/reports/tests/ClientReportAutoCompleteInputBox.test.tsx > ClientReportAutoCompleteInputBox > handles selection of an option
TypeError: mockFetchSuggestions.mockResolvedValue is not a function
 ❯ src/modules/edit/client-information/reports/tests/ClientReportAutoCompleteInputBox.test.tsx:148:26       
    146|       { reportId: 999, name: 'SelectedReport', fileExt: 'CSV' },
    147|     ];
    148|     mockFetchSuggestions.mockResolvedValue(mockData);
       |                          ^
    149|
    150|     render(

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯[4/4]⎯


 Test Files  1 failed (1)
      Tests  4 failed | 2 passed (6)
   Start at  23:12:24
   Duration  5.25s

 FAIL  Tests failed. Watching for file changes...
       press h to show help, press q to quit
