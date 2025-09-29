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

ModuleRegistry.registerModules([AllCommunityModule, InfiniteRowModelModule]);

const EmailEventIdPage = () => {
  const containerStyle = useMemo(() => ({ width: '100%', height: 520 }), []);
  const gridStyle = useMemo(() => ({ height: '100%', width: '100%' }), []);
  const defaultColDef = useMemo(() => ({ flex: 1, minWidth: 100 }), []);
  const rowSelection = useMemo(() => ({ mode: 'multiRow' }), []);
  const gridApi = useRef(null);

  const [pageSize, setPageSize] = useState(10);
  const [searchValue, setSearchValue] = useState('');

  const [applicationDropdownOptions, setApplicationDropdownOptions] = useState([]);
  const [eventIdDropdownOptions, setEventIdDropdownOptions] = useState([]);

  const [selectedRows, setSelectedRows] = useState([]);
  const [deleteSnackbarOpen, setDeleteSnackbarOpen] = useState(false);
  const [selectedRowToDelete, setSelectedRowToDelete] = useState(null);
  const [snackbarType, setSnackbarType] = useState('none');

  const [dialogOpen, setDialogOpen] = useState(false);
  const [dialogRow, setDialogRow] = useState(null);
  const [dialogMode, setDialogMode] = useState('addEdit');

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
                handleDeleteClick(params.data);
              }}
            />
          </span>
        </div>
      ),
    },
  ]);

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

  // ---------- Helper: client-side filter for fallback ----------
  const clientFilter = (rows, query) => {
    const q = (query || '').trim().toLowerCase();
    if (!q) return rows;
    return rows.filter((r) => {
      const eventId = String(r.eventId ?? '').toLowerCase();
      const applicationId = String(r.applicationId ?? '').toLowerCase();
      const eventTxt = String(r.eventTxt ?? '').toLowerCase();
      return (
        eventId.includes(q) ||
        applicationId.includes(q) ||
        eventTxt.includes(q)
      );
    });
  };

  // ---------- DataSource with server-side search + client fallback ----------
  const createDataSource = (pageSizeArg, query) => ({
    getRows: (params) => {
      const page = params.startRow / pageSizeArg;

      emailEventIdService
        .fetchEmailEventIds(page, pageSizeArg, query) // pass query to API
        .then((data) => {
          // Support both shapes: { rows, totalElements } or simple array
          const resp = data?.response?.EventIdList;
          let rawRows = [];
          let total = undefined;

          if (resp && Array.isArray(resp.rows) && typeof resp.totalElements === 'number') {
            rawRows = resp.rows;
            total = resp.totalElements;
          } else if (Array.isArray(resp) && typeof resp.totalElements === 'number') {
            rawRows = resp;
            total = resp.totalElements;
          } else if (Array.isArray(resp)) {
            rawRows = resp;
          }

          // If backend ignores filtering, ensure client-side fallback kicks in:
          const filtered = clientFilter(rawRows, query);

          // Derive lastRow in a way that works both when we know the true total
          // and when we don't (let grid keep asking blocks with -1).
          let lastRow;
          if (typeof total === 'number') {
            // If server provided a total, use it (assumed already filtered by query).
            lastRow = total;
          } else {
            // No total: if this block has < pageSize rows after filtering,
            // signal "no more" to grid; else let it request the next block.
            lastRow = filtered.length < pageSizeArg ? params.startRow + filtered.length : -1;
          }

          params.successCallback(filtered, lastRow);
        })
        .catch(() => {
          params.failCallback();
        });
    },
  });

  const dataSource = useMemo(() => createDataSource(pageSize, searchValue), [pageSize, searchValue]);

  const onGridReady = (params) => {
    gridApi.current = params.api;
    params.api.setGridOption('datasource', dataSource);
  };

  useEffect(() => {
    if (!gridApi.current) return;
    gridApi.current.setGridOption('datasource', dataSource);
    gridApi.current.refreshInfiniteCache(); // reload blocks when search/pageSize changes
  }, [dataSource]);

  const handleSearchChange = (value) => {
    setSearchValue(value); // triggers datasource rebuild + refresh
  };

  const handleAddClick = () => {
    setDialogRow(null);
    setDialogMode('addEdit');
    setDialogOpen(true);
  };

  const handleEditClick = (rowData) => {
    setDialogRow(rowData);
    setDialogMode('addEdit');
    setDialogOpen(true);
  };

  const handleDeleteClick = (rowData) => {
    setSelectedRowToDelete(rowData);
    setDialogRow(rowData);
    setDialogMode('delete');
    setDialogOpen(true);
  };

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
          selectedRowToDelete.applicationId
        );
      }
      setSnackbarType('delete-confirmation');
      gridApi.current?.refreshInfiniteCache();
      setSelectedRows([]);
    } catch {}
  };

  const handleDialogSuccess = (type) => {
    setDialogOpen(false);
    setDialogRow(null);
    setDialogMode('addEdit');
    setSnackbarType(type);
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

      {/* Dialog */}
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

      {/* Bulk Delete confirm */}
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
