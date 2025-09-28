import React, { useState, useMemo, useRef, useEffect } from 'react';
import { Button } from '@mui/material';
import { EditIcon, ClientMappingDeleteIcon, SearchIcon, ApplicationEventId } from '../../../assets/brand/svg-constants';
import { CFormTextarea, CFormInput, CFormSelect } from '@coreui/react';
import * as emailEventIdService from '../../../services/AdminReportService/EmailEventIdService';
import { AgGridReact } from 'ag-grid-react';
import { ModuleRegistry, AllCommunityModule, InfiniteRowModelModule } from 'ag-grid-community';
import CustomSnackbar from '../../../components/CustomSnackbar';
import EmailEventIdDialog from './EmailEventIdDialog';
import '../../../scss/EmailEventId.scss';

// Register AG Grid community + infinite row model
ModuleRegistry.registerModules([AllCommunityModule, InfiniteRowModelModule]);

const EmailEventIdPage = () => {
  // Layout
  const containerStyle = useMemo(() => ({ width: '100%', height: 520 }), []);
  const gridStyle = useMemo(() => ({ height: '100%', width: '100%' }), []);
  const defaultColDef = useMemo(() => ({ flex: 1, minWidth: 100 }), []);
  const rowSelection = useMemo(() => ({ mode: 'multiRow' }), []);
  const gridApi = useRef(null);

  // Grid paging
  const [pageSize, setPageSize] = useState(10);

  // Toolbar / search
  const [searchValue, setSearchValue] = useState('');

  // Dropdowns for the dialog
  const [applicationDropdownOptions, setApplicationDropdownOptions] = useState([]);
  const [eventIdDropdownOptions, setEventIdDropdownOptions] = useState([]);

  // Selection / snackbars
  const [selectedRows, setSelectedRows] = useState([]);
  const [deleteSnackbarOpen, setDeleteSnackbarOpen] = useState(false);
  const [selectedRowToDelete, setSelectedRowToDelete] = useState(null);
  // 'none' | 'add' | 'update' | 'delete-confirmation'
  const [snackbarType, setSnackbarType] = useState('none');

  // Add/Edit dialog state
  const [dialogOpen, setDialogOpen] = useState(false);
  const [dialogRow, setDialogRow] = useState(null); // null for Add, object for Edit

  // Columns
  const [columnDefs] = useState([
    { field: 'eventId', headerName: 'Event ID', maxWidth: 120 },
    { field: 'applicationId', headerName: 'Application', maxWidth: 180 },
    { field: 'eventTxt', headerName: 'Description', minWidth: 200, flex: 2 },
    {
      headerName: '',
      field: 'actions',
      minWidth: 120,
      cellClass: 'actions-cell-flex-end',
      cellRenderer: (params) => (
        <div className="actions-cell icon-container action-cell-flex">
          <span className="icon-wrapper">
            <EditIcon
              style={{ cursor: 'pointer' }}
              onClick={(e) => {
                e?.stopPropagation?.();
                handleEditClick(params.data);
              }}
            />
          </span>
          <span className="icon-wrapper">
            <ClientMappingDeleteIcon
              data-testid="delete-icon"
              style={{ cursor: 'pointer' }}
              onClick={(e) => {
                e?.stopPropagation?.();
                handleDeleteClick(params.data);
              }}
            />
          </span>
        </div>
      ),
    },
  ]);

  // Init dropdowns
  useEffect(() => {
    (async () => {
      try {
        const appRes = await emailEventIdService.fetchApplicationIdList();
        setApplicationDropdownOptions(appRes.response?.ApplicationIdList ?? []);
      } catch {}
      try {
        const evtRes = await emailEventIdService.fetchEventIdList();
        setEventIdDropdownOptions(evtRes.response?.EventIdList ?? []);
      } catch {}
    })();
  }, []);

  // DataSource (Infinite Row Model)
  const createDataSource = (pageSizeArg) => ({
    getRows: (params) => {
      const page = params.startRow / pageSizeArg;
      emailEventIdService
        .fetchEmailEventIds(page, pageSizeArg)
        .then((data) => {
          const response = data.response?.EventIdList;
          let rows, lastRow;
          if (response && Array.isArray(response.rows) && typeof response.totalElements === 'number') {
            rows = response.rows;
            lastRow = response.totalElements;
          } else if (Array.isArray(response) && typeof response.totalElements === 'number') {
            rows = response;
            lastRow = response.totalElements;
          } else if (Array.isArray(response)) {
            rows = response;
            lastRow = rows.length < pageSizeArg ? params.startRow + rows.length : -1;
          } else {
            rows = [];
            lastRow = 0;
          }
          params.successCallback(rows, lastRow);
        })
        .catch(() => {
          params.failCallback();
        });
    },
  });

  const dataSource = useMemo(() => createDataSource(pageSize), [pageSize]);

  // Grid handlers
  const onGridReady = (params) => {
    gridApi.current = params.api;
  };

  const handleSearchChange = (value) => {
    setSearchValue(value);
    gridApi.current?.setQuickFilter(value);
  };

  // Dialog open helpers
  const handleAddClick = () => {
    setDialogRow(null);      // Add mode
    setDialogOpen(true);
  };

  const handleEditClick = (rowData) => {
    setDialogRow(rowData);   // Edit mode
    setDialogOpen(true);
  };

  const handleDeleteClick = (rowData) => {
    setSelectedRowToDelete(rowData);
    setDeleteSnackbarOpen(true);
  };

  // Delete (single or bulk)
  const onHandleDeleteConfirm = async () => {
    setDeleteSnackbarOpen(false);
    try {
      if (selectedRows.length > 1) {
        for (const r of selectedRows) {
          await emailEventIdService.deleteEmailEventId(r.eventId, r.applicationId);
        }
      } else if (selectedRowToDelete?.eventId) {
        await emailEventIdService.deleteEmailEventId(
          selectedRowToDelete.eventId,
          selectedRowToDelete.applicationId,
        );
      }
      setSnackbarType('delete-confirmation');
      gridApi.current?.refreshInfiniteCache();
      setSelectedRows([]);
    } catch (err) {
      // optionally show error
    }
  };

  const handleDialogSuccess = (type) => {
    // Close child, refresh, show status
    setDialogOpen(false);
    setDialogRow(null);
    setSnackbarType(type); // 'add' | 'update'
    gridApi.current?.refreshInfiniteCache();
  };

  const handleSnackbarCancel = () => {
    setDeleteSnackbarOpen(false);
    setSnackbarType('none');
  };

  return (
    <div className="email-event-id-page email-event-id-dialog">
      {/* Header */}
      <div className="eei-header" style={{ display: 'flex', alignItems: 'center', gap: 8, marginBottom: 12 }}>
        <div className="application-event-id-icon">
          <ApplicationEventId />
        </div>
        <h2 style={{ margin: 0 }}>Trace Admin Application Event Id</h2>
      </div>

      {/* Toolbar */}
      <div className="action-container" style={{ display: 'flex', alignItems: 'center', gap: 12, margin: '8px 0 20px' }}>
        {selectedRows.length > 1 ? (
          <div style={{ display: 'flex', alignItems: 'center', gap: 8 }}>
            <span className="text-count">{selectedRows.length} rows selected</span>
            <Button variant="outlined" size="small" onClick={() => setDeleteSnackbarOpen(true)}>
              Delete
            </Button>
          </div>
        ) : (
          <div className="search-input">
            <CFormInput
              placeholder="Search"
              value={searchValue}
              onChange={(e) => handleSearchChange(e.target.value)}
            />
            <span className="search-icon">
              <SearchIcon style={{ cursor: 'pointer' }} onClick={() => handleSearchChange(searchValue)} />
            </span>
          </div>
        )}
        <div style={{ marginLeft: 'auto' }}>
          <Button
            variant="contained"
            size="small"
            onClick={(e) => {
              e.stopPropagation();
              handleAddClick();
            }}
          >
            Add
          </Button>
        </div>
      </div>

      {/* Grid */}
      <div className="ag-theme-quartz" style={containerStyle}>
        <div style={gridStyle}>
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
            onSelectionChanged={(params) => {
              setSelectedRows(params.api.getSelectedRows());
            }}
            onPaginationChanged={(params) => {
              const newPageSize = params.api.paginationGetPageSize();
              setPageSize((prev) => (prev !== newPageSize ? newPageSize : prev));
            }}
          />
        </div>
      </div>

      {/* Child dialog (Add/Edit) */}
      <EmailEventIdDialog
        open={dialogOpen}
        onClose={() => {
          setDialogOpen(false);
          setDialogRow(null);
        }}
        selectedRow={dialogRow}
        applicationOptions={applicationDropdownOptions}
        eventIdOptions={eventIdDropdownOptions}
        onSuccess={handleDialogSuccess}
      />

      {/* Delete confirm */}
      <CustomSnackbar
        type="delete"
        open={deleteSnackbarOpen}
        onClose={handleSnackbarCancel}
        handleOk={onHandleDeleteConfirm}
        title="Confirm Delete"
        body={
          selectedRows.length > 1
            ? 'Are you sure you want to delete the selected rows?'
            : 'Are you sure you want to delete this row?'
        }
      />

      {/* Status snackbars */}
      <CustomSnackbar
        type={snackbarType}
        open={snackbarType !== 'none'}
        handleOk={handleSnackbarCancel}
        onClose={handleSnackbarCancel}
        title="Application Event Id"
        body={
          snackbarType === 'add'
            ? 'You have successfully Added a new event'
            : snackbarType === 'update'
            ? 'You have successfully Updated an event'
            : snackbarType === 'delete-confirmation'
            ? 'You have successfully Deleted'
            : ''
        }
      />
    </div>
  );
};

export default EmailEventIdPage;
