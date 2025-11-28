<AgGridReact<NavigationRow>
  rowData={tableData}
  columnDefs={columnDefs}
  defaultColDef={defaultColDef}
  rowClassRules={rowClassRules}
  pagination={false}
  suppressScrollOnNewData={true}
  animateRows={true}
  onGridReady={(params) => {
    gridApiRef.current = params.api;
  }}
  onRowClicked={handleRowClicked}
  getRowId={(params) => {
    const d = params.data as NavigationRow;
    if (d?.isGroup) return `g:${d.client}`;
    return `s:${d.client}:${d.sysPrin}`;
  }}
/>
