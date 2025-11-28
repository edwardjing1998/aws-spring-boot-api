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
                  deltaRowDataMode={true}



                  o overload matches this call.
  Overload 1 of 2, '(props: AgGridReactProps<NavigationRow>): AgGridReact<NavigationRow>', gave the following error.
    Type '{ rowData: NavigationRow[]; columnDefs: ColDef<NavigationRow, any>[]; defaultColDef: ColDef<NavigationRow, any>; rowClassRules: RowClassRules<...>; ... 6 more ...; deltaRowDataMode: boolean; }' is not assignable to type 'IntrinsicAttributes & IntrinsicClassAttributes<AgGridReact<NavigationRow>> & Readonly<AgGridReactProps<NavigationRow>>'.
      Property 'deltaRowDataMode' does not exist on type 'IntrinsicAttributes & IntrinsicClassAttributes<AgGridReact<NavigationRow>> & Readonly<AgGridReactProps<NavigationRow>>'.
  Overload 2 of 2, '(props: AgGridReactProps<NavigationRow>, context: any): AgGridReact<NavigationRow>', gave the following error.
