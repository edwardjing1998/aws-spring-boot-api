/* Remove the outer left/right borders on the AG Grid while keeping horizontal lines */
.no-side-borders {
  /* keep normal row/header borders unless you change these */
  --ag-borders: solid;

  /* kill the outer frame’s side borders */
  .ag-root-wrapper {
    border-left: 0 !important;
    border-right: 0 !important;
  }

  /* some themes also add side borders on header/body containers */
  .ag-header, 
  .ag-body-viewport {
    border-left: 0 !important;
    border-right: 0 !important;
  }
}



<div className="ag-theme-quartz no-side-borders" style={containerStyle}>
  <div style={gridStyle}>
    <AgGridReact
      /* ...your props stay the same... */
    />
  </div>
</div>
