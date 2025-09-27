/* Remove LEFT/RIGHT outer borders around the grid */
.ag-theme-quartz.no-grid-border,
.ag-theme-quartz.no-grid-border .ag-root-wrapper,
.ag-theme-quartz.no-grid-border .ag-root,
.ag-theme-quartz.no-grid-border .ag-header,
.ag-theme-quartz.no-grid-border .ag-body-viewport,
.ag-theme-quartz.no-grid-border .ag-center-cols-viewport {
  border-left: 0 !important;
  border-right: 0 !important;
  box-shadow: none; /* just in case the theme adds a subtle edge shadow */
}

/* If the CoreUI card is showing side borders, remove those too */
.daily-activity-wrapper .card {
  border-left: 0 !important;
  border-right: 0 !important;
}

/* (Optional) also nuke any border on the grid container itself */
.ag-grid-container {
  border-left: 0 !important;
  border-right: 0 !important;
}
