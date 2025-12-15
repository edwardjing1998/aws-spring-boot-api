 npm test -- src/modules/edit/client-information/reports/tests/ClientReportAutoCompleteInputBox.test.tsx

> @coreui/fiserv-rapid-admin@5.4.0 test
> vitest src/modules/edit/client-information/reports/tests/ClientReportAutoCompleteInputBox.test.tsx        


 DEV  v3.2.4 C:/Users/F2LIPBX/react/fiserv-github/react-rapid-admin

 ❯ src/modules/edit/client-information/reports/tests/ClientReportAutoCompleteInputBox.test.tsx (3 tests | 1 failed) 5192ms
   ✓ ClientReportAutoCompleteInputBox > renders the input field correctly 84ms
   ✓ ClientReportAutoCompleteInputBox > handles input changes calling the parent setter 89ms
   × ClientReportAutoCompleteInputBox > triggers the API call after the debounce period 5018ms
     → Test timed out in 5000ms.
If this is a long-running test, pass a timeout value as the last argument or configure it globally with "testTimeout".

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯ Failed Tests 1 ⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯

 FAIL  src/modules/edit/client-information/reports/tests/ClientReportAutoCompleteInputBox.test.tsx > ClientReportAutoCompleteInputBox > triggers the API call after the debounce period
Error: Test timed out in 5000ms.
If this is a long-running test, pass a timeout value as the last argument or configure it globally with "testTimeout".
 ❯ src/modules/edit/client-information/reports/tests/ClientReportAutoCompleteInputBox.test.tsx:60:5
     58|
     59|     // Add this inside your describe block fro mutation testing
     60|     it('triggers the API call after the debounce period', async () => {
       |     ^
     61|     mockFetchSuggestions.mockResolvedValue({ data: [] }); // Setup the mock return
     62|

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯[1/1]⎯


 Test Files  1 failed (1)
      Tests  1 failed | 2 passed (3)
   Start at  07:38:16
   Duration  19.55s (transform 183ms, setup 0ms, collect 4.28s, tests 5.19s, environment 3.32s, prepare 940ms)

 FAIL  Tests failed. Watching for file changes...
       press h to show help, press q to quit

 *  History restored 

PS C:\Users\F2LIPBX\react\fiserv-github\react-rapid-admin> 


