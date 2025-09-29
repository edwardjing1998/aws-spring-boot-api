import React, { useState, useMemo, useRef, useEffect } from 'react';
import { ModuleRegistry, AllCommunityModule } from 'ag-grid-community';
import { AgGridReact } from 'ag-grid-react';
import { Button } from '@mui/material';
import { CFormTextarea, CFormInput, CFormCheck, CFormSelect } from '@coreui/react';
import CustomSnackbar from '../../../components/CustomSnackbar';
import { EditIcon, ClientMappingDeleteIcon, SearchIcon } from '../../../assets/brand/svg-constants';
import { clientReportMappingData } from './data';
import '../../../scss/ClientReportMapping.scss';
import ClientReportMappingDialog from './ClientReportMappingDialog'; // ⬅️ dialog

// Register AG Grid community module
ModuleRegistry.registerModules([AllCommunityModule]);

const ClientReportMapping = () => {
  const containerStyle = useMemo(() => ({ width: '100%', height: 500 }), []);
  const gridStyle = useMemo(() => ({ height: '100%', width: '100%' }), []);
  const defaultColDef = useMemo(() => ({ flex: 1, minWidth: 100, sortable: true, filter: true }), []);

  const gridApi = useRef(null);

  // Client-side data
  const [rowData, setRowData] = useState([]);

  useEffect(() => {
    const rows = clientReportMappingData?.response?.clientReportMapping ?? [];
    setRowData(rows.map((r, i) => ({ id: i + 1, ...r })));
  }, []);

  // Dialog state
  const [dialogOpen, setDialogOpen] = useState(false);
  const [dialogMode, setDialogMode] = useState('addEdit'); // 'addEdit' | 'delete'
  const [selectedRecord, setSelectedRecord] = useState(null);

  // Columns
  const [columnDefs] = useState([
    { field: 'clientId', headerName: 'Client', maxWidth: 120 },
    { field: 'reportClientId', headerName: 'Report Client', maxWidth: 160 },
    {
      field: 'sendToBothInd',
      headerName: 'Send To Both',
      maxWidth: 140,
      valueGetter: (p) => (p.data?.sendToBothInd ? 'Yes' : 'No'),
    },
    { field: 'description', headerName: 'Description', minWidth: 200, flex: 2 },
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
              style={{ cursor: 'pointer' }}
              onClick={() => handleDeleteClick(params.data)}
            />
          </span>
        </div>
      ),
    },
  ]);

  // Toolbar & form state
  const [selectedRows, setSelectedRows] = useState([]);
  const [snackbarType, setSnackbarType] = useState('none'); // 'add' | 'update' | 'delete-confirmation' | 'none'
  const [enableAddContent, setEnableAddContent] = useState(false);
  const [searchValue, setSearchValue] = useState('');

  // (inline form kept for compatibility; safe to remove if unused)
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

  const onHandleClear = () => {
    setOriginalData(null);
    setSelectedRows([]);
    setData({ clientId: '', reportClientId: '', sendToBothInd: false, application: '', description: '' });
  };

  // OPEN dialog for Add
  const handleAddClick = () => {
    setSelectedRecord(null);
    setDialogMode('addEdit');
    setDialogOpen(true);
  };

  // OPEN dialog for Edit
  const handleEditClick = (row) => {
    const [application, ...rest] = (row.description || '').split('-');
    const form = {
      clientId: row.clientId ?? '',
      reportClientId: row.reportClientId ?? '',
      sendToBothInd: !!row.sendToBothInd,
      application: application || '',
      description: rest.join('-').trim() || row.description || '',
    };
    setSelectedRecord(form);
    setDialogMode('addEdit');
    setDialogOpen(true);
  };

  // OPEN dialog for Delete (instead of deleting immediately)
  const handleDeleteClick = (row) => {
    const [application, ...rest] = (row.description || '').split('-');
    const form = {
      clientId: row.clientId ?? '',
      reportClientId: row.reportClientId ?? '',
      sendToBothInd: !!row.sendToBothInd,
      application: application || '',
      description: rest.join('-').trim() || row.description || '',
    };
    setSelectedRecord(form);
    setDialogMode('delete');
    setDialogOpen(true);
  };

  // Dialog close
  const handleDialogClose = () => {
    setDialogOpen(false);
  };

  // Dialog save success
  const handleDialogSuccess = (type, payload) => {
    if (type === 'add') {
      const newRow = {
        clientId: payload.clientId,
        reportClientId: payload.reportClientId,
        sendToBothInd: payload.sendToBothInd,
        description: `${payload.application ? payload.application + '-' : ''}${payload.description}`,
      };
      setRowData((prev) => [newRow, ...prev]);
      setSnackbarType('add');
    } else if (type === 'update') {
      setRowData((prev) =>
        prev.map((r) =>
          r.clientId === payload.clientId && r.reportClientId === payload.reportClientId
            ? {
                ...r,
                sendToBothInd: payload.sendToBothInd,
                description: `${payload.application ? payload.application + '-' : ''}${payload.description}`,
              }
            : r,
        ),
      );
      setSnackbarType('update');
    }
    setDialogOpen(false);
  };

  // Dialog delete confirm
  const handleDialogDeleteConfirm = () => {
    const s = selectedRecord;
    if (!s) return;
    setRowData((prev) =>
      prev.filter((r) => !(r.clientId === s.clientId && r.reportClientId === s.reportClientId)),
    );
    setSnackbarType('delete-confirmation');
    setDialogOpen(false);
  };

  const handleSearchChange = (value) => {
    setSearchValue(value);
    gridApi.current?.setQuickFilter(value);
  };

  return (
    <div className="client-report-mapping-page client-report-mapping-dialog">
      {/* Toolbar */}
      {!enableAddContent && (
        <div className="crm-toolbar action-container" style={{ padding: '12px 0 50px' }}>
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
          <div>
            <Button variant="contained" size="small" onClick={handleAddClick}>
              Add
            </Button>
          </div>
        </div>
      )}

      {/* Content */}
      <div className="crm-content">
        <div className="ag-theme-quartz" style={containerStyle}>
          <div style={gridStyle}>
            <AgGridReact
              rowData={rowData}
              columnDefs={columnDefs}
              defaultColDef={defaultColDef}
              pagination={true}
              paginationPageSize={10}
              paginationPageSizeSelector={[10, 20, 50, 100]}
              rowSelection="multiple"
              onGridReady={(params) => {
                gridApi.current = params.api;
              }}
              onSelectionChanged={(p) => setSelectedRows(p.api.getSelectedRows())}
            />
          </div>
        </div>
      </div>

      {/* Dialog */}
      <ClientReportMappingDialog
        open={dialogOpen}
        onClose={handleDialogClose}
        selectedRecord={selectedRecord}
        onSuccess={handleDialogSuccess}
        onDeleteConfirm={handleDialogDeleteConfirm}     // ⬅️ NEW
        mode={dialogMode}                               // ⬅️ NEW
        applicationOptions={applicationDropdownOptions}
      />

      {/* Snackbars */}
      <CustomSnackbar
        type={snackbarType}
        open={snackbarType !== 'none'}
        handleOk={() => setSnackbarType('none')}
        onClose={() => setSnackbarType('none')}
        title={
          snackbarType === 'add' || snackbarType === 'update' || snackbarType === 'delete-confirmation'
            ? 'Client Report Mapping'
            : ''
        }
        body={
          snackbarType === 'add'
            ? 'You have successfully Added a new client'
            : snackbarType === 'update'
            ? 'You have successfully Updated a client'
            : snackbarType === 'delete-confirmation'
            ? 'You have successfully Deleted a client'
            : ''
        }
      />
    </div>
  );
};

export default ClientReportMapping;
