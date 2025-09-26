import React, { useState, useMemo, useRef, useEffect } from 'react';
import { ModuleRegistry, AllCommunityModule } from 'ag-grid-community';
import { AgGridReact } from 'ag-grid-react';
import { Button } from '@mui/material';
import { CFormTextarea, CFormInput, CFormCheck, CFormSelect } from '@coreui/react';
import CustomSnackbar from '../../../components/CustomSnackbar';
import { EditIcon, ClientMappingDeleteIcon, SearchIcon } from '../../../assets/brand/svg-constants';
import { clientReportMappingData } from './data';
import '../../../scss/ClientReportMapping.scss';
import ClientReportMappingDialog from './ClientReportMappingDialog'; // ⬅️ NEW import

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
  const [selectedRecord, setSelectedRecord] = useState(null);

  // Columns for your JSON
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
  const [enableAddContent, setEnableAddContent] = useState(false); // (kept in case you still use inline form later)
  const [searchValue, setSearchValue] = useState('');

  // If you no longer need inline add/edit, you can remove this inline form state
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
    setDialogOpen(true);
  };

  // OPEN dialog for Edit
  const handleEditClick = (row) => {
    // Split description into application + description (optional)
    const [application, ...rest] = (row.description || '').split('-');
    const form = {
      clientId: row.clientId ?? '',
      reportClientId: row.reportClientId ?? '',
      sendToBothInd: !!row.sendToBothInd,
      application: application || '',
      description: rest.join('-').trim() || row.description || '',
    };
    setSelectedRecord(form);
    setDialogOpen(true);
  };

  // DELETE (client-side for demo)
  const handleDeleteClick = (row) => {
    setRowData((prev) => prev.filter((r) => !(r.clientId === row.clientId && r.reportClientId === row.reportClientId)));
    setSnackbarType('delete-confirmation');
  };

  // Dialog close
  const handleDialogClose = () => {
    setDialogOpen(false);
  };

  // Dialog save success
  const handleDialogSuccess = (type, payload) => {
    // payload is { clientId, reportClientId, sendToBothInd, application, description }
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





import React, { useEffect, useState } from 'react';
import { Dialog, DialogTitle, DialogContent, DialogActions, Button, IconButton } from '@mui/material';
import { CloseIcon } from '../../../assets/brand/svg-constants';
import { CFormInput, CFormSelect, CFormCheck, CFormTextarea } from '@coreui/react';
import CustomSnackbar from '../../../components/CustomSnackbar';
import '../../../scss/ClientReportMapping.scss';

const ClientReportMappingDialog = ({
  open,
  onClose,
  selectedRecord,             // { clientId, reportClientId, sendToBothInd, application, description } | null
  onSuccess,                   // (type: 'add'|'update', payload) => void
  applicationOptions = [],     // [{label, value}, ...]
}) => {
  const [form, setForm] = useState({
    clientId: '',
    reportClientId: '',
    sendToBothInd: false,
    application: '',
    description: '',
  });

  const [original, setOriginal] = useState(null);
  const [snackbarType, setSnackbarType] = useState('none');

  useEffect(() => {
    if (!open) return;
    if (selectedRecord) {
      setForm({
        clientId: selectedRecord.clientId || '',
        reportClientId: selectedRecord.reportClientId || '',
        sendToBothInd: !!selectedRecord.sendToBothInd,
        application: selectedRecord.application || '',
        description: selectedRecord.description || '',
      });
      setOriginal({
        clientId: selectedRecord.clientId || '',
        reportClientId: selectedRecord.reportClientId || '',
        sendToBothInd: !!selectedRecord.sendToBothInd,
        application: selectedRecord.application || '',
        description: selectedRecord.description || '',
      });
    } else {
      setForm({
        clientId: '',
        reportClientId: '',
        sendToBothInd: false,
        application: '',
        description: '',
      });
      setOriginal(null);
    }
    setSnackbarType('none');
  }, [open, selectedRecord]);

  const handleClose = (e, reason) => {
    if (reason === 'backdropClick' || reason === 'escapeKeyDown') return;
    onClose?.();
  };

  const handleChange = (key, value) => {
    setForm((prev) => ({ ...prev, [key]: value }));
  };

  const isAdd = !original;
  const canSave = () => {
    // Basic validation
    if (!form.clientId || form.clientId.length !== 4) return false;
    if (!form.reportClientId || form.reportClientId.length !== 4) return false;
    if (!form.description.trim()) return false;

    if (!isAdd) {
      return JSON.stringify(form) !== JSON.stringify(original);
    }
    return true;
  };

  const handleSave = () => {
    // Example business rule: prevent "send to both" when ids equal
    if (form.clientId === form.reportClientId && form.sendToBothInd) {
      setSnackbarType('info');
      return;
    }

    if (isAdd) {
      onSuccess?.('add', { ...form });
    } else {
      onSuccess?.('update', { ...form });
    }
  };

  const handleSnackbarCancel = () => setSnackbarType('none');

  return (
    <>
      <Dialog open={open} onClose={handleClose} PaperProps={{ className: 'client-report-mapping-dialog' }}>
        <DialogTitle>Client Report Mapping</DialogTitle>
        <IconButton aria-label="close" onClick={handleClose}>
          <CloseIcon />
        </IconButton>

        <DialogContent dividers>
          <div className="add-main-container">
            <div className="row-container">
              <div className="clientId-container client-field">
                <span className="label">Client ID</span>
                <CFormInput
                  type="text"
                  maxLength={4}
                  value={form.clientId}
                  onChange={(e) => handleChange('clientId', e.target.value)}
                  className="clientId-input"
                  disabled={!isAdd} /* lock ids during edit */
                />
              </div>
              <div className="clientId-container client-field">
                <span className="label">Report Client ID</span>
                <CFormInput
                  type="text"
                  maxLength={4}
                  value={form.reportClientId}
                  onChange={(e) => handleChange('reportClientId', e.target.value)}
                  className="clientId-input"
                  disabled={!isAdd}
                />
              </div>
            </div>

            <div className="row-container">
              <div className="clientId-container client-field">
                <span className="label">Application</span>
                <CFormSelect
                  className="clientId-input"
                  value={form.application}
                  onChange={(e) => handleChange('application', e.target.value)}
                >
                  {applicationOptions.map((o) => (
                    <option key={o.value} value={o.value}>
                      {o.label}
                    </option>
                  ))}
                </CFormSelect>
              </div>
              <div className="send-to-both-container">
                <CFormCheck
                  className="send-to-both"
                  checked={form.sendToBothInd}
                  onChange={(e) => handleChange('sendToBothInd', e.target.checked)}
                />
                <span>Send to Both</span>
              </div>
            </div>

            <div className="second-row-container">
              <div className="clientId-container">
                <span>Description</span>
                <CFormTextarea
                  value={form.description}
                  onChange={(e) => handleChange('description', e.target.value)}
                  className="description-textarea"
                />
              </div>
            </div>
          </div>
        </DialogContent>

        <DialogActions>
          <div className="client-report-mapping-button-container">
            <Button variant="outlined" size="small" onClick={handleClose}>
              Cancel
            </Button>
            <Button variant="contained" size="small" disabled={!canSave()} onClick={handleSave}>
              Save
            </Button>
          </div>
        </DialogActions>
      </Dialog>

      <CustomSnackbar
        type={snackbarType}
        open={snackbarType !== 'none'}
        handleOk={handleSnackbarCancel}
        onClose={handleSnackbarCancel}
        title={snackbarType === 'info' ? 'Confirm Action' : ''}
        body={
          snackbarType === 'info'
            ? 'Send To Both is invalid when Client ID equals Report Client ID'
            : ''
        }
      />
    </>
  );
};

export default ClientReportMappingDialog;
