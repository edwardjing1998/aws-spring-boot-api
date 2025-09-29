// File: src/views/rapid-admin-report/email-event-id/EmailEventIdPage.jsx
import React, { useState, useMemo, useRef, useEffect } from 'react';
import { Button } from '@mui/material';
import { EditIcon, ClientMappingDeleteIcon, SearchIcon, ApplicationEventId } from '../../../assets/brand/svg-constants';
import { CFormInput } from '@coreui/react';
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

  // Add/Edit/Delete dialog state
  const [dialogOpen, setDialogOpen] = useState(false);
  const [dialogRow, setDialogRow] = useState(null); // null for Add, object for Edit/Delete
  const [dialogMode, setDialogMode] = useState('addEdit'); // 'addEdit' | 'delete'

  // Columns
  const [columnDefs] = useState([
    { field: 'eventId', headerName: 'Event ID', maxWidth: 250 },
    { field: 'applicationId', headerName: 'Application', maxWidth: 250 },
    { field: 'eventTxt', headerName: 'Description', minWidth: 420, flex: 2 },
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
                handleDeleteClick(params.data); // open dialog in delete mode
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

  // DataSource (Infinite Row Model) — now includes `query` for server-side search
  const createDataSource = (pageSizeArg, query) => ({
    getRows: (params) => {
      const page = params.startRow / pageSizeArg;
      emailEventIdService
        // IMPORTANT: add `query` (searchValue) to the request your API expects
        .fetchEmailEventIds(page, pageSizeArg, query)
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

  // Rebuild data source when page size OR search changes
  const dataSource = useMemo(() => createDataSource(pageSize, searchValue), [pageSize, searchValue]);

  // Grid handlers
  const onGridReady = (params) => {
    gridApi.current = params.api;
    // set initial datasource
    params.api.setGridOption('datasource', dataSource);
  };

  // When dataSource changes, update the grid and refresh cache
  useEffect(() => {
    if (!gridApi.current) return;
    gridApi.current.setGridOption('datasource', dataSource);
    // Clear current cache and load with new query
    gridApi.current.refreshInfiniteCache();
  }, [dataSource]);

  const handleSearchChange = (value) => {
    setSearchValue(value); // triggers dataSource rebuild -> grid refreshes blocks with query
  };

  // Dialog open helpers
  const handleAddClick = () => {
    setDialogRow(null);      // Add mode
    setDialogMode('addEdit');
    setDialogOpen(true);
  };

  const handleEditClick = (rowData) => {
    setDialogRow(rowData);   // Edit mode
    setDialogMode('addEdit');
    setDialogOpen(true);
  };

  const handleDeleteClick = (rowData) => {
    setSelectedRowToDelete(rowData);
    setDialogRow(rowData);   // Delete mode uses the same dialog, locked fields
    setDialogMode('delete');
    setDialogOpen(true);
  };

  // Bulk delete (toolbar button)
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
    } catch {
      // optionally surface an error
    }
  };

  const handleDialogSuccess = (type) => {
    setDialogOpen(false);
    setDialogRow(null);
    setDialogMode('addEdit');
    setSnackbarType(type); // 'add' | 'update' | 'delete-confirmation'
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
            // datasource is set via onGridReady + effect when it changes
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

      {/* Child dialog (Add/Edit/Delete) */}
      <EmailEventIdDialog
        open={dialogOpen}
        onClose={() => {
          setDialogOpen(false);
          setDialogRow(null);
          setDialogMode('addEdit');
        }}
        selectedRow={dialogRow}
        applicationOptions={applicationDropdownOptions}
        eventIdOptions={eventIdDropdownOptions}
        onSuccess={handleDialogSuccess}
        mode={dialogMode}
      />

      {/* Bulk Delete confirm (snackbar) */}
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
