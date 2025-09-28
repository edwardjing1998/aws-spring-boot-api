import React, { useState, useMemo, useRef, useEffect } from 'react';
import { Button } from '@mui/material';
import { CloseIcon, EditIcon, ClientMappingDeleteIcon, SearchIcon, ApplicationEventId } from '../../../assets/brand/svg-constants';
import { CFormTextarea, CFormInput, CFormSelect } from '@coreui/react';
import * as emailEventIdService from '../../../services/AdminReportService/EmailEventIdService';
import { AgGridReact } from 'ag-grid-react';
import { ModuleRegistry, AllCommunityModule, InfiniteRowModelModule } from 'ag-grid-community';
import CustomSnackbar from '../../../components/CustomSnackbar';
import '../../../scss/EmailEventId.scss';

// ✅ Register AG Grid Community + Infinite Row Model (v33+ requires this explicitly)
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

  // Dropdowns
  const [applicationDropdownOptions, setApplicationDropdownOptions] = useState([]);
  const [eventIdDropdownOptions, setEventIdDropdownOptions] = useState([]);

  // Form state
  const [enableAddContent, setEnableAddContent] = useState(false);
  const [data, setData] = useState({
    eventId: '',
    applicationId: '',
    eventTxt: '',
  });
  const [originalData, setOriginalData] = useState(null);

  // Selection / snackbars
  const [selectedRows, setSelectedRows] = useState([]);
  const [deleteSnackbarOpen, setDeleteSnackbarOpen] = useState(false);
  const [selectedRowToDelete, setSelectedRowToDelete] = useState(null);
  // 'none' | 'add' | 'update' | 'delete-confirmation'
  const [snackbarType, setSnackbarType] = useState('none');

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
            <EditIcon style={{ cursor: 'pointer' }} onClick={() => handleEditClick(params.data)} />
          </span>
          <span className="icon-wrapper">
            <ClientMappingDeleteIcon
              data-testid="delete-icon"
              style={{ cursor: 'pointer' }}
              onClick={() => handleDeleteClick(params.data)}
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
        setApplicationDropdownOptions(appRes.response.ApplicationIdList ?? []);
      } catch {}
      try {
        const evtRes = await emailEventIdService.fetchEventIdList();
        setEventIdDropdownOptions(evtRes.response.EventIdList ?? []);
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
          const response = data.response.EventIdList;
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

  // Form helpers
  const onHandleClear = () => {
    setOriginalData(null);
    setData({ eventId: '', applicationId: '', eventTxt: '' });
    setSelectedRows([]);
  };

  const handleAddClick = () => {
    onHandleClear();
    setEnableAddContent(true);
  };

  const handleEditClick = (rowData) => {
    setEnableAddContent(true);
    setData({
      eventId: rowData.eventId ?? '',
      applicationId: rowData.applicationId ?? '',
      eventTxt: rowData.eventTxt ?? '',
    });
    setOriginalData({
      eventId: rowData.eventId ?? '',
      applicationId: rowData.applicationId ?? '',
      eventTxt: rowData.eventTxt ?? '',
    });
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
        // bulk delete each
        for (const r of selectedRows) {
          await emailEventIdService.deleteEmailEventId(r.eventId, r.applicationId);
        }
      } else if (selectedRowToDelete?.eventId) {
        await emailEventIdService.deleteEmailEventId(selectedRowToDelete.eventId, selectedRowToDelete.applicationId);
      }
      setSnackbarType('delete-confirmation');
      gridApi.current?.refreshInfiniteCache();
      onHandleClear();
    } catch (err) {
      // optionally show error
    }
  };

  const isDataChanged = () => {
    if (!originalData) {
      return data.applicationId.trim() !== '' && data.eventTxt.trim() !== '' && data.eventId.trim() !== '';
    }
    return JSON.stringify(data) !== JSON.stringify(originalData);
  };

  const handleSave = async () => {
    try {
      const req = {
        applicationId: data.applicationId.trim(),
        eventId: data.eventId.trim(),
        eventTxt: data.eventTxt.trim(),
      };
      if (originalData) {
        await emailEventIdService.updateEmailEventId(req);
        setSnackbarType('update');
      } else {
        await emailEventIdService.initiateEmailEventId(req);
        setSnackbarType('add');
      }
      setEnableAddContent(false);
      setTimeout(() => onHandleClear(), 200);
      gridApi.current?.refreshInfiniteCache();
    } catch (err) {
      // optionally show error
    }
  };

  const handleCancelForm = () => {
    setEnableAddContent(false);
    onHandleClear();
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
      {!enableAddContent && (
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
            <Button variant="contained" size="small" onClick={handleAddClick}>
              Add
            </Button>
          </div>
        </div>
      )}

      {/* Content */}
      <div className="eei-content">
        {enableAddContent ? (
          <>
            <h3 style={{ margin: '0 0 12px' }}>{originalData ? 'Edit Event Id' : 'Add Event Id'}</h3>
            <div className="add-main-container">
              <div className="row-container">
                <div className="eventId-container eventId-field">
                  <span className="label">Event ID</span>
                  <CFormSelect
                    className="eventId-input applicationId-input"
                    value={data.eventId}
                    onChange={(e) => setData({ ...data, eventId: e.target.value })}
                  >
                    <option value=""></option>
                    {eventIdDropdownOptions.map((option) => (
                      <option key={option} value={option}>
                        {option}
                      </option>
                    ))}
                  </CFormSelect>
                </div>

                <div className="eventId-container eventId-field">
                  <span className="label">Application</span>
                  <CFormSelect
                    className="eventId-input applicationId-input"
                    value={data.applicationId}
                    onChange={(e) => setData({ ...data, applicationId: e.target.value })}
                  >
                    <option value=""></option>
                    {applicationDropdownOptions.map((option) => (
                      <option key={option} value={option}>
                        {option}
                      </option>
                    ))}
                  </CFormSelect>
                </div>
              </div>

              <div className="row-container">
                <div className="description-container eventId-field" style={{ width: '100%' }}>
                  <span className="label">Description</span>
                  <CFormTextarea
                    className="eventId-input description-input"
                    value={data.eventTxt}
                    onChange={(e) => setData({ ...data, eventTxt: e.target.value })}
                  />
                </div>
              </div>

              <div className="add-action-container email-event-id-button-container" style={{ marginTop: 12 }}>
                <Button variant="outlined" size="small" onClick={handleCancelForm}>
                  Cancel
                </Button>
                <Button variant="contained" size="small" disabled={!isDataChanged()} onClick={handleSave}>
                  Save
                </Button>
              </div>
            </div>
          </>
        ) : (
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
        )}
      </div>

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
        title={
          snackbarType === 'add'
            ? 'Application Event Id'
            : snackbarType === 'update'
            ? 'Application Event Id'
            : snackbarType === 'delete-confirmation'
            ? 'Application Event Id'
            : ''
        }
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
