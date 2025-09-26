import React, { useState, useEffect, useMemo, useRef } from 'react';
import { ModuleRegistry, AllCommunityModule } from 'ag-grid-community';
import { AgGridReact } from 'ag-grid-react';
import { Button } from '@mui/material';
import { CFormTextarea, CFormInput, CFormCheck, CFormSelect } from '@coreui/react';
import * as clientReportSvc from '../../../services/AdminMaintenanceService/ClientReportMappingService';
import CustomSnackbar from '../../../components/CustomSnackbar';
import { EditIcon, ClientMappingDeleteIcon, SearchIcon } from '../../../assets/brand/svg-constants';
import '../../../scss/ClientReportMapping.scss';

ModuleRegistry.registerModules([AllCommunityModule]);

const ClientReportMapping = () => {
  const containerStyle = useMemo(() => ({ width: '100%', height: '89%' }), []);
  const gridStyle = useMemo(() => ({ height: '100%', width: '100%' }), []);
  const defaultColDef = useMemo(() => ({ flex: 1, minWidth: 100 }), []);
  const rowSelection = useMemo(() => ({ mode: 'multiRow' }), []);

  // 🔁 columns (WeekDaysRenderer removed)
  const [columnDefs] = useState([
    { field: 'aspId', headerName: '#', minWidth: 75 },
    { field: 'activeInactive', headerName: 'Indicator', minWidth: 97 },
    { field: 'name', headerName: 'Name' },
    { field: 'emailAddress', headerName: 'Email' },
    { field: 'application', headerName: 'Application' },
    { field: 'startHour', headerName: 'Start Hr', minWidth: 50 },
    { field: 'endHour', headerName: 'End Hr', minWidth: 50 },
    {
      field: 'days',
      headerName: 'Week Days',
      minWidth: 150,
      cellClass: 'days-cell',
      valueGetter: (p) => {
        const d = p.data?.days;
        if (!d) return '';
        // array: ['Mon','Tue']
        if (Array.isArray(d)) return d.join(', ');
        // string: 'Mon,Wed,Fri'
        if (typeof d === 'string') return d;
        // object of booleans: { mon:true, tue:false, ... }
        if (typeof d === 'object') {
          const map = {
            mon: 'Mon',
            tue: 'Tue',
            wed: 'Wed',
            thu: 'Thu',
            fri: 'Fri',
            sat: 'Sat',
            sun: 'Sun',
          };
          return Object.keys(map)
            .filter((k) => d[k])
            .map((k) => map[k])
            .join(', ');
        }
        return '';
      },
    },
    {
      headerName: '',
      minWidth: 127,
      field: 'actions',
      cellClass: 'actions-cell-flex-end',
      cellRenderer: (params) => (
        <div className="actions-cell icon-container action-cell-flex">
          <span className="icon-wrapper">
            <EditIcon style={{ cursor: 'pointer' }} onClick={() => params.context.handleEditClick(params.data)} />
          </span>
          <span className="icon-wrapper">
            <ClientMappingDeleteIcon
              data-testid="delete-icon"
              style={{ cursor: 'pointer' }}
              onClick={() => params.context.handleDeleteClick(params.data)}
            />
          </span>
        </div>
      ),
    },
  ]);

  const gridApi = useRef(null);
  const [selectedRows, setSelectedRows] = useState([]);
  const [deleteSnackbarOpen, setDeleteSnackbarOpen] = useState(false);
  const [snackbarType, setSnackbarType] = useState('none');
  const [searchValue, setSearchValue] = useState('');
  const [refreshKey, setRefreshKey] = useState(0);
  const [selectedRowToDelete, setSelectedRowToDelete] = useState(null);
  const [pageSize, setPageSize] = useState(10);
  const [dataSource, setDataSource] = useState(() => createDataSource(pageSize));
  const [enableAddContent, setEnableAddContent] = useState(false);

  const [data, setData] = useState({
    clientId: '',
    reportClientId: '',
    sendToBothInd: false,
    application: '',
    description: '',
  });
  const [originalData, setOriginalData] = useState(null);

  const applicationDropdownOptions = [
    { label: '', value: '' },
    { label: 'Dialer', value: 'Dialer' },
    { label: 'Rapid', value: 'Rapid' },
  ];

  function onHandleClear() {
    setOriginalData(null);
    setSelectedRows([]);
    setData({ clientId: '', reportClientId: '', sendToBothInd: false, application: '', description: '' });
  }

  function createDataSource(pageSizeArg) {
    return {
      getRows: (params) => {
        const page = params.startRow / pageSizeArg;
        clientReportSvc
          .fetchClientReportMappings(page, pageSizeArg)
          .then((resp) => {
            const payload = resp.response.clientReportMapping;
            let rows, lastRow;
            if (payload && Array.isArray(payload.rows) && typeof payload.totalElements === 'number') {
              rows = payload.rows;
              lastRow = payload.totalElements;
            } else if (Array.isArray(payload) && typeof payload.totalElements === 'number') {
              rows = payload;
              lastRow = payload.totalElements;
            } else if (Array.isArray(payload)) {
              rows = payload;
              lastRow = rows.length < pageSizeArg ? params.startRow + rows.length : -1;
            } else {
              rows = [];
              lastRow = 0;
            }
            params.successCallback(rows, lastRow);
          })
          .catch(() => params.failCallback());
      },
    };
  }

  useEffect(() => {
    setDataSource(() => createDataSource(pageSize));
    setRefreshKey((k) => k + 1);
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, [pageSize]);

  const handleAddClick = () => {
    onHandleClear();
    setEnableAddContent(true);
  };

  const onHandleDelete = async () => {
    setDeleteSnackbarOpen(false);
    try {
      if (selectedRows.length > 1) {
        const filtered = selectedRows.map(({ clientId, description }) => ({ clientId, description }));
        await clientReportSvc.deleteAllClientReportMapping(filtered);
      } else if (selectedRowToDelete?.clientId) {
        const application = selectedRowToDelete.description.split('-')[0];
        await clientReportSvc.deleteClientReportMapping(selectedRowToDelete.clientId, application);
      }
      onHandleClear();
      setSnackbarType('delete-confirmation');
      gridApi.current?.refreshInfiniteCache();
    } catch (err) {
      // handle error as needed
    }
  };

  const handleEditClick = (rowData) => {
    setEnableAddContent(true);
    const [application, description] = rowData.description.split('-');
    const formData = {
      clientId: rowData.clientId,
      reportClientId: rowData.reportClientId,
      sendToBothInd: rowData.sendToBothInd,
      application,
      description,
    };
    setData(formData);
    setOriginalData(formData);
  };

  const handleDeleteClick = (rowData) => {
    setSelectedRowToDelete(rowData);
    setDeleteSnackbarOpen(true);
  };

  const handleSave = async () => {
    if (originalData) {
      await onUpdate();
    } else {
      await onAdd();
    }
  };

  const onUpdate = async () => {
    if (data.clientId === data.reportClientId && data.sendToBothInd) {
      setSnackbarType('info');
      return;
    }
    try {
      await clientReportSvc.updateClientReportMapping(data);
      setSnackbarType('update');
      setTimeout(() => onHandleClear(), 500);
      gridApi.current?.refreshInfiniteCache();
      setEnableAddContent(false);
    } catch (err) {}
  };

  const onAdd = async () => {
    if (data.clientId === data.reportClientId && data.sendToBothInd) {
      setSnackbarType('info');
      return;
    }
    try {
      await clientReportSvc.addClientReportMapping(data);
      setSnackbarType('add');
      setTimeout(() => onHandleClear(), 500);
      gridApi.current?.refreshInfiniteCache();
      setEnableAddContent(false);
    } catch (err) {}
  };

  const isDataChanged = () => {
    if (!originalData) {
      return (
        data.clientId.length === 4 &&
        data.reportClientId.length === 4 &&
        data.application.trim() !== '' &&
        data.description.trim() !== '' &&
        (data.clientId !== data.reportClientId ? data.sendToBothInd : true)
      );
    }
    if (data.clientId.length !== 4 || data.reportClientId.length !== 4) return false;
    return JSON.stringify(data) !== JSON.stringify(originalData);
  };

  const handleCancelForm = () => {
    setEnableAddContent(false);
    onHandleClear();
  };

  const handleSearchChange = async (value) => {
    setSearchValue(value);
    if (value.trim() === '') {
      setDataSource(() => createDataSource(pageSize));
      setRefreshKey((k) => k + 1);
      return;
    }
    const searchDataSource = {
      getRows: (params) => {
        clientReportSvc
          .searchByClientId(value)
          .then((result) => {
            const resultData = result.response.clientReportMapping || [];
            params.successCallback(resultData, resultData.length);
          })
          .catch(() => params.failCallback());
      },
    };
    setDataSource(() => searchDataSource);
    setRefreshKey((k) => k + 1);
  };

  return (
    <div className="client-report-mapping-page client-report-mapping-dialog">
      {/* Toolbar */}
      {!enableAddContent && (
        <div className="crm-toolbar action-container" style={{ padding: '12px 0' }}>
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
          <div>
            <Button variant="contained" size="small" onClick={handleAddClick}>
              Add
            </Button>
          </div>
        </div>
      )}

      {/* Content */}
      <div className="crm-content">
        {enableAddContent ? (
          <>
            <h2 style={{ margin: '0 0 12px' }}>Add / Edit Client Report Mapping</h2>
            <div className="add-main-container">
              <div className="row-container">
                <div className="clientId-container client-field">
                  <span className="label" data-testid="client-id-label">Client ID</span>
                  <CFormInput
                    type="text"
                    value={data.clientId}
                    onChange={(e) => setData({ ...data, clientId: e.target.value })}
                    maxLength={4}
                    className="clientId-input"
                  />
                </div>
                <div className="clientId-container client-field">
                  <span className="label" data-testid="report-client-id-label">Report Client ID</span>
                  <CFormInput
                    type="text"
                    value={data.reportClientId}
                    onChange={(e) => setData({ ...data, reportClientId: e.target.value })}
                    maxLength={4}
                    className="clientId-input"
                  />
                </div>
              </div>

              <div className="row-container">
                <div className="clientId-container client-field">
                  <span className="label">Application</span>
                  <CFormSelect
                    className="clientId-input"
                    value={data.application}
                    onChange={(e) => setData({ ...data, application: e.target.value })}
                  >
                    {applicationDropdownOptions.map((option) => (
                      <option key={option.value} value={option.value}>{option.label}</option>
                    ))}
                  </CFormSelect>
                </div>
                <div className="send-to-both-container">
                  <CFormCheck
                    className="send-to-both"
                    disabled={data.clientId === data.reportClientId}
                    checked={data.sendToBothInd}
                    onChange={(e) => setData({ ...data, sendToBothInd: e.target.checked })}
                  />
                  <span>Send to Both</span>
                </div>
              </div>

              <div className="second-row-container">
                <div className="clientId-container">
                  <span data-testid="description-label">Description</span>
                  <CFormTextarea
                    value={data.description}
                    onChange={(e) => setData({ ...data, description: e.target.value })}
                    className="description-textarea"
                  />
                </div>
              </div>

              <div className="add-action-container client-report-mapping-button-container" style={{ marginTop: 12 }}>
                <Button variant="outlined" size="small" onClick={handleCancelForm}>Cancel</Button>
                <Button variant="contained" size="small" disabled={!isDataChanged()} onClick={handleSave}>Save</Button>
              </div>
            </div>
          </>
        ) : (
          <div style={containerStyle} className="ag-theme-quartz">
            <div style={gridStyle}>
              <AgGridReact
                key={refreshKey}
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
                onGridReady={(params) => { gridApi.current = params.api; }}
                datasource={dataSource}
                onSelectionChanged={(params) => setSelectedRows(params.api.getSelectedRows())}
                onPaginationChanged={(params) => {
                  const newPageSize = params.api.paginationGetPageSize();
                  setPageSize((prev) => (prev !== newPageSize ? newPageSize : prev));
                }}
              />
            </div>
          </div>
        )}
      </div>

      {/* Snackbars */}
      <CustomSnackbar
        type="delete"
        open={deleteSnackbarOpen}
        onClose={() => setDeleteSnackbarOpen(false)}
        handleOk={onHandleDelete}
        title="Confirm Delete"
        body="Are you sure you want to delete?"
      />
      <CustomSnackbar
        type={snackbarType}
        open={snackbarType !== 'none'}
        handleOk={() => setSnackbarType('none')}
        onClose={() => setSnackbarType('none')}
        title={
          snackbarType === 'add' || snackbarType === 'update' || snackbarType === 'delete-confirmation'
            ? 'Client Report Mapping'
            : snackbarType === 'info'
            ? 'Confirm Action'
            : ''
        }
        body={
          snackbarType === 'add'
            ? 'You have successfully Added a new client'
            : snackbarType === 'update'
            ? 'You have successfully Updated a client'
            : snackbarType === 'delete-confirmation'
            ? 'You have successfully Deleted a client'
            : snackbarType === 'info'
            ? 'Send To Both is invalid when Client ID equals Report Client ID'
            : ''
        }
      />
    </div>
  );
};

export default ClientReportMapping;
