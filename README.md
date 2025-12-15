src/modules/edit/client-information/reports/tests/ClientReportAutoCompleteInputBox.test.tsx x1 
        Filename pattern: src/modules/edit/client-information/reports/tests/ClientReportAutoCompleteInputBox.test.tsx

 ❯ src/modules/edit/client-information/reports/tests/ClientReportAutoCompleteInputBox.test.tsx (6 tests | 5 failed) 141ms
   ✓ ClientReportAutoCompleteInputBox > renders the input field correctly 85ms
   × ClientReportAutoCompleteInputBox > fetches suggestions when user types after debounce delay 15ms       
     → mockFetchSuggestions.mockResolvedValue is not a function
   × ClientReportAutoCompleteInputBox > displays options formatted correctly 2ms
     → mockFetchSuggestions.mockResolvedValue is not a function
   × ClientReportAutoCompleteInputBox > handles wildcard selection correctly 1ms
     → mockFetchSuggestions.mockResolvedValue is not a function
   × ClientReportAutoCompleteInputBox > handles input changes calling the parent setter 34ms
     → Found multiple elements with the placeholder text of: Search Report

Here are the matching elements:

Ignored nodes: comments, script, style
<input
  aria-autocomplete="list"
  aria-expanded="false"
  aria-invalid="false"
  autocapitalize="none"
  autocomplete="off"
  class="MuiInputBase-input MuiOutlinedInput-input MuiInputBase-inputTypeSearch MuiInputBase-inputSizeSmall MuiInputBase-inputAdornedEnd MuiAutocomplete-input MuiAutocomplete-inputFocused css-w0tvxy-MuiInputBase-input-MuiOutlinedInput-input"
  id="«r0»"
  placeholder="Search Report"
  role="combobox"
  spellcheck="false"
  type="search"
  value=""
/>

Ignored nodes: comments, script, style
<input
  aria-autocomplete="list"
  aria-expanded="false"
  aria-invalid="false"
  autocapitalize="none"
  autocomplete="off"
  class="MuiInputBase-input MuiOutlinedInput-input MuiInputBase-inputTypeSearch MuiInputBase-inputSizeSmall MuiInputBase-inputAdornedEnd MuiAutocomplete-input MuiAutocomplete-inputFocused css-w0tvxy-MuiInputBase-input-MuiOutlinedInput-input"
  id="«r2»"
  placeholder="Search Report"
  role="combobox"
  spellcheck="false"
  type="search"
  value=""
/>

(If this is intentional, then use the `*AllBy*` variant of the query (like `queryAllByText`, `getAllByText`, or `findAllByText`)).

Ignored nodes: comments, script, style
<body>
  <div>
    <div
      class="MuiAutocomplete-root MuiAutocomplete-fullWidth css-56xrgl-MuiAutocomplete-root"
    >
      <div
        class="MuiFormControl-root MuiFormControl-fullWidth MuiTextField-root css-cmpglg-MuiFormControl-root-MuiTextField-root"
      >
        <div
          class="MuiInputBase-root MuiOutlinedInput-root MuiInputBase-colorPrimary MuiInputBase-fullWidth MuiInputBase-formControl MuiInputBase-sizeSmall MuiInputBase-adornedEnd MuiAutocomplete-inputRoot css-h6cdxm-MuiInputBase-root-MuiOutlinedInput-root"
        >
          <input
            aria-autocomplete="list"
            aria-expanded="false"
            aria-invalid="false"
            autocapitalize="none"
            autocomplete="off"
            class="MuiInputBase-input MuiOutlinedInput-input MuiInputBase-inputTypeSearch MuiInputBase-inputSizeSmall MuiInputBase-inputAdornedEnd MuiAutocomplete-input MuiAutocomplete-inputFocused css-w0tvxy-MuiInputBase-input-MuiOutlinedInput-input"
            id="«r0»"
            placeholder="Search Report"
            role="combobox"
            spellcheck="false"
            type="search"
            value=""
          />
          <div
            class="MuiInputAdornment-root MuiInputAdornment-positionEnd MuiInputAdornment-outlined MuiInputAdornment-sizeSmall css-elo8k2-MuiInputAdornment-root"
          >
            <svg
              aria-hidden="true"
              class="MuiSvgIcon-root MuiSvgIcon-fontSizeMedium css-1jsc7vi-MuiSvgIcon-root"
              data-testid="SearchIcon"
              focusable="false"
              viewBox="0 0 24 24"
            >
              <path
                d="M15.5 14h-.79l-.28-.27C15.41 12.59 16 11.11 16 9.5 16 5.91 13.09 3 9.5 3S3 5.91 3 9.5 5.91 16 9.5 16c1.61 0 3.09-.59 4.23-1.57l.27.28v.79l5 4.99L20.49 19zm-6 0C7.01 14 5 11.99 5 9.5S7.01 5 9.5 5 14 7.01 14 9.5 11.99 14 9.5 14"
              />
            </svg>
          </div>
          <fieldset
            aria-hidden="true"
            class="MuiOutlinedInput-notchedOutline css-1ll44ll-MuiOutlinedInput-notchedOutline"
          >
            <legend
              class="css-w4cd9x"
            >
              <span
                aria-hidden="true"
                class="notranslate"
              >
                
              </span>
            </legend>
          </fieldset>
        </div>
      </div>
    </div>
  </div>
  <div>
    <div
      class="MuiAutocomplete-root MuiAutocomplete-fullWidth css-56xrgl-MuiAutocomplete-root"
    >
      <div
        class="MuiFormControl-root MuiFormControl-fullWidth MuiTextField-root css-cmpglg-MuiFormControl-root-MuiTextField-root"
      >
        <div
          class="MuiInputBase-root MuiOutlinedInput-root MuiInputBase-colorPrimary MuiInputBase-fullWidth MuiInputBase-formControl MuiInputBase-sizeSmall MuiInputBase-adornedEnd MuiAutocomplete-inputRoot css-h6cdxm-MuiInputBase-root-MuiOutlinedInput-root"
        >
          <input
            aria-autocomplete="list"
            aria-expanded="false"
            aria-invalid="false"
            autocapitalize="none"
            autocomplete="off"
            class="MuiInputBase-input MuiOutlinedInput-input MuiInputBase-inputTypeSearch MuiInputBase-inputSizeSmall MuiInputBase-inputAdornedEnd MuiAutocomplete-input MuiAutocomplete-inputFocused css-w0tvxy-MuiInputBase-input-MuiOutlinedInput-input"
            id="«r2»"
            placeholder="Search Report"
            role="combobox"
            spellcheck="false"
            type="search"
            value=""
          />
          <div
            class="MuiInputAdornment-root MuiInputAdornment-positionEnd MuiInputAdornment-outlined MuiInputAdornment-sizeSmall css-elo8k2-MuiInputAdornment-root"
          >
            <svg
              aria-hidden="true"
              class="MuiSvgIcon-root MuiSvgIcon-fontSizeMedium css-1jsc7vi-MuiSvgIcon-root"
              data-testid="SearchIcon"
              focusable="false"
              viewBox="0 0 24 24"
            >
              <path
                d="M15.5 14h-.79l-.28-.27C15.41 12.59 16 11.11 16 9.5 16 5.91 13.09 3 9.5 3S3 5.91 3 9.5 5.91 16 9.5 16c1.61 0 3.09-.59 4.23-1.57l.27.28v.79l5 4.99L20.49 19zm-6 0C7.01 14 5 11.99 5 9.5S7.01 5 9.5 5 14 7.01 14 9.5 11.99 14 9.5 14"
              />
            </svg>
          </div>
          <fieldset
            aria-hidden="true"
            class="MuiOutlinedInput-notchedOutline css-1ll44ll-MuiOutlinedInput-notchedOutline"
          >
            <legend
              class="css-w4cd9x"
            >
              <span
                aria-hidden="true"
                class="notranslate"
              >
                
              </span>
            </legend>
          </fieldset>
        </div>
      </div>
    </div>
  </div>
</body>
   × ClientReportAutoCompleteInputBox > handles selection of an option 1ms
     → mockFetchSuggestions.mockResolvedValue is not a function
                                                                                                            
⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯ Failed Tests 5 ⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯
                                                                                                            
 FAIL  src/modules/edit/client-information/reports/tests/ClientReportAutoCompleteInputBox.test.tsx > ClientReportAutoCompleteInputBox > fetches suggestions when user types after debounce delay                        
TypeError: mockFetchSuggestions.mockResolvedValue is not a function
 ❯ src/modules/edit/client-information/reports/tests/ClientReportAutoCompleteInputBox.test.tsx:48:26
     46|       { reportId: 102, name: 'Test Report B', fileExt: 'XLS' },
     47|     ];
     48|     mockFetchSuggestions.mockResolvedValue(mockData);
       |                          ^
     49|
     50|     render(

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯[1/5]⎯

 FAIL  src/modules/edit/client-information/reports/tests/ClientReportAutoCompleteInputBox.test.tsx > ClientReportAutoCompleteInputBox > displays options formatted correctly
TypeError: mockFetchSuggestions.mockResolvedValue is not a function
 ❯ src/modules/edit/client-information/reports/tests/ClientReportAutoCompleteInputBox.test.tsx:81:26        
     79|       { reportId: 101, name: 'ReportA', fileExt: 'PDF' },
     80|     ];
     81|     mockFetchSuggestions.mockResolvedValue(mockData);
       |                          ^
     82|
     83|     render(

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯[2/5]⎯

 FAIL  src/modules/edit/client-information/reports/tests/ClientReportAutoCompleteInputBox.test.tsx > ClientReportAutoCompleteInputBox > handles wildcard selection correctly
TypeError: mockFetchSuggestions.mockResolvedValue is not a function
 ❯ src/modules/edit/client-information/reports/tests/ClientReportAutoCompleteInputBox.test.tsx:108:26       
    106|   it('handles wildcard selection correctly', async () => {
    107|     const mockData = [{ reportId: 1, name: 'WildcardMatch' }];
    108|     mockFetchSuggestions.mockResolvedValue(mockData);
       |                          ^
    109|
    110|     render(

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯[3/5]⎯

 FAIL  src/modules/edit/client-information/reports/tests/ClientReportAutoCompleteInputBox.test.tsx > ClientReportAutoCompleteInputBox > handles input changes calling the parent setter
TestingLibraryElementError: Found multiple elements with the placeholder text of: Search Report

Here are the matching elements:

Ignored nodes: comments, script, style
<input
  aria-autocomplete="list"
  aria-expanded="false"
  aria-invalid="false"
  autocapitalize="none"
  autocomplete="off"
  class="MuiInputBase-input MuiOutlinedInput-input MuiInputBase-inputTypeSearch MuiInputBase-inputSizeSmall MuiInputBase-inputAdornedEnd MuiAutocomplete-input MuiAutocomplete-inputFocused css-w0tvxy-MuiInputBase-input-MuiOutlinedInput-input"
  id="«r0»"
  placeholder="Search Report"
  role="combobox"
  spellcheck="false"
  type="search"
  value=""
/>

Ignored nodes: comments, script, style
<input
  aria-autocomplete="list"
  aria-expanded="false"
  aria-invalid="false"
  autocapitalize="none"
  autocomplete="off"
  class="MuiInputBase-input MuiOutlinedInput-input MuiInputBase-inputTypeSearch MuiInputBase-inputSizeSmall MuiInputBase-inputAdornedEnd MuiAutocomplete-input MuiAutocomplete-inputFocused css-w0tvxy-MuiInputBase-input-MuiOutlinedInput-input"
  id="«r2»"
  placeholder="Search Report"
  role="combobox"
  spellcheck="false"
  type="search"
  value=""
/>

(If this is intentional, then use the `*AllBy*` variant of the query (like `queryAllByText`, `getAllByText`, or `findAllByText`)).

Ignored nodes: comments, script, style
<body>
  <div>
    <div
      class="MuiAutocomplete-root MuiAutocomplete-fullWidth css-56xrgl-MuiAutocomplete-root"
    >
      <div
        class="MuiFormControl-root MuiFormControl-fullWidth MuiTextField-root css-cmpglg-MuiFormControl-root-MuiTextField-root"
      >
        <div
          class="MuiInputBase-root MuiOutlinedInput-root MuiInputBase-colorPrimary MuiInputBase-fullWidth MuiInputBase-formControl MuiInputBase-sizeSmall MuiInputBase-adornedEnd MuiAutocomplete-inputRoot css-h6cdxm-MuiInputBase-root-MuiOutlinedInput-root"
        >
          <input
            aria-autocomplete="list"
            aria-expanded="false"
            aria-invalid="false"
            autocapitalize="none"
            autocomplete="off"
            class="MuiInputBase-input MuiOutlinedInput-input MuiInputBase-inputTypeSearch MuiInputBase-inputSizeSmall MuiInputBase-inputAdornedEnd MuiAutocomplete-input MuiAutocomplete-inputFocused css-w0tvxy-MuiInputBase-input-MuiOutlinedInput-input"
            id="«r0»"
            placeholder="Search Report"
            role="combobox"
            spellcheck="false"
            type="search"
            value=""
          />
          <div
            class="MuiInputAdornment-root MuiInputAdornment-positionEnd MuiInputAdornment-outlined MuiInputAdornment-sizeSmall css-elo8k2-MuiInputAdornment-root"
          >
            <svg
              aria-hidden="true"
              class="MuiSvgIcon-root MuiSvgIcon-fontSizeMedium css-1jsc7vi-MuiSvgIcon-root"
              data-testid="SearchIcon"
              focusable="false"
              viewBox="0 0 24 24"
            >
              <path
                d="M15.5 14h-.79l-.28-.27C15.41 12.59 16 11.11 16 9.5 16 5.91 13.09 3 9.5 3S3 5.91 3 9.5 5.91 16 9.5 16c1.61 0 3.09-.59 4.23-1.57l.27.28v.79l5 4.99L20.49 19zm-6 0C7.01 14 5 11.99 5 9.5S7.01 5 9.5 5 14 7.01 14 9.5 11.99 14 9.5 14"
              />
            </svg>
          </div>
          <fieldset
            aria-hidden="true"
            class="MuiOutlinedInput-notchedOutline css-1ll44ll-MuiOutlinedInput-notchedOutline"
          >
            <legend
              class="css-w4cd9x"
            >
              <span
                aria-hidden="true"
                class="notranslate"
              >
                ​
              </span>
            </legend>
          </fieldset>
        </div>
      </div>
    </div>
  </div>
  <div>
    <div
      class="MuiAutocomplete-root MuiAutocomplete-fullWidth css-56xrgl-MuiAutocomplete-root"
    >
      <div
        class="MuiFormControl-root MuiFormControl-fullWidth MuiTextField-root css-cmpglg-MuiFormControl-root-MuiTextField-root"
      >
        <div
          class="MuiInputBase-root MuiOutlinedInput-root MuiInputBase-colorPrimary MuiInputBase-fullWidth MuiInputBase-formControl MuiInputBase-sizeSmall MuiInputBase-adornedEnd MuiAutocomplete-inputRoot css-h6cdxm-MuiInputBase-root-MuiOutlinedInput-root"
        >
          <input
            aria-autocomplete="list"
            aria-expanded="false"
            aria-invalid="false"
            autocapitalize="none"
            autocomplete="off"
            class="MuiInputBase-input MuiOutlinedInput-input MuiInputBase-inputTypeSearch MuiInputBase-inputSizeSmall MuiInputBase-inputAdornedEnd MuiAutocomplete-input MuiAutocomplete-inputFocused css-w0tvxy-MuiInputBase-input-MuiOutlinedInput-input"
            id="«r2»"
            placeholder="Search Report"
            role="combobox"
            spellcheck="false"
            type="search"
            value=""
          />
          <div
            class="MuiInputAdornment-root MuiInputAdornment-positionEnd MuiInputAdornment-outlined MuiInputAdornment-sizeSmall css-elo8k2-MuiInputAdornment-root"
          >
            <svg
              aria-hidden="true"
              class="MuiSvgIcon-root MuiSvgIcon-fontSizeMedium css-1jsc7vi-MuiSvgIcon-root"
              data-testid="SearchIcon"
              focusable="false"
              viewBox="0 0 24 24"
            >
              <path
                d="M15.5 14h-.79l-.28-.27C15.41 12.59 16 11.11 16 9.5 16 5.91 13.09 3 9.5 3S3 5.91 3 9.5 5.91 16 9.5 16c1.61 0 3.09-.59 4.23-1.57l.27.28v.79l5 4.99L20.49 19zm-6 0C7.01 14 5 11.99 5 9.5S7.01 5 9.5 5 14 7.01 14 9.5 11.99 14 9.5 14"
              />
            </svg>
          </div>
          <fieldset
            aria-hidden="true"
            class="MuiOutlinedInput-notchedOutline css-1ll44ll-MuiOutlinedInput-notchedOutline"
          >
            <legend
              class="css-w4cd9x"
            >
              <span
                aria-hidden="true"
                class="notranslate"
              >
                ​
              </span>
            </legend>
          </fieldset>
        </div>
      </div>
    </div>
  </div>
</body>
 ❯ Object.getElementError node_modules/@testing-library/dom/dist/config.js:37:19
 ❯ getElementError node_modules/@testing-library/dom/dist/query-helpers.js:20:35
 ❯ getMultipleElementsFoundError node_modules/@testing-library/dom/dist/query-helpers.js:23:10
 ❯ node_modules/@testing-library/dom/dist/query-helpers.js:55:13
 ❯ node_modules/@testing-library/dom/dist/query-helpers.js:95:19
 ❯ src/modules/edit/client-information/reports/tests/ClientReportAutoCompleteInputBox.test.tsx:140:26       
    138|     );
    139|
    140|     const input = screen.getByPlaceholderText('Search Report');
       |                          ^
    141|     fireEvent.change(input, { target: { value: 'A' } });
    142|

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯[4/5]⎯

 FAIL  src/modules/edit/client-information/reports/tests/ClientReportAutoCompleteInputBox.test.tsx > ClientReportAutoCompleteInputBox > handles selection of an option
TypeError: mockFetchSuggestions.mockResolvedValue is not a function
 ❯ src/modules/edit/client-information/reports/tests/ClientReportAutoCompleteInputBox.test.tsx:150:26       
    148|       { reportId: 999, name: 'SelectedReport', fileExt: 'CSV' },
    149|     ];
    150|     mockFetchSuggestions.mockResolvedValue(mockData);
       |                          ^
    151|
    152|     render(

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯[5/5]⎯


 Test Files  1 failed (1)
      Tests  5 failed | 1 passed (6)
   Start at  23:04:15
   Duration  4.73s

 FAIL  Tests failed. Watching for file changes...
       press h to show help, press q to quit
