<AgGridReact
  columnDefs={columnDefs}
  rowModelType="infinite"
  defaultColDef={defaultColDef}
  rowSelection={rowSelection}
  pagination={true}
  paginationPageSize={pageSize}
  paginationPageSizeSelector={[10, 20, 50, 100]}
  cacheBlockSize={pageSize}
  infiniteInitialRowCount={pageSize}
  cacheOverflowSize={1}
  maxConcurrentDatasourceRequests={1}
  context={{ handleEditClick, handleDeleteClick }}
  maxBlocksInCache={2}
  onGridReady={onGridReady}
  datasource={dataSource}
  onSelectionChanged={(params) => setSelectedRows(params.api.getSelectedRows())}
  onPaginationChanged={(params) => {
    const newPageSize = params.api.paginationGetPageSize();
    setPageSize((prev) => (prev !== newPageSize ? newPageSize : prev));
  }}

  {/* 🔎 make toolbar search filter across ALL columns */}
  quickFilterText={searchValue}
  getQuickFilterText={(params) => {
    const r = params.data || {};
    return [
      r.eventId,
      r.applicationId,
      r.eventTxt,
    ]
      .filter(Boolean)
      .join(' ')
      .toLowerCase();
  }}
/>
