import React, { useState, useMemo, useRef, useEffect } from 'react';
import { ModuleRegistry, AllCommunityModule } from 'ag-grid-community';
import { AgGridReact } from 'ag-grid-react';
import { Button } from '@mui/material';
import { CFormInput } from '@coreui/react';
import CustomSnackbar from '../../../components/CustomSnackbar';
import { EditIcon, ClientMappingDeleteIcon, SearchIcon } from '../../../assets/brand/svg-constants';
import { clientReportMappingData } from './data';
import '../../../scss/ClientReportMapping.scss';
import ClientReportMappingDialog from './ClientReportMappingDialog'; // dialog

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

  // Toolbar & search
  const [selectedRows, setSelectedRows] = useState([]);
  const [snackbarType, setSnackbarType] = useState('none'); // 'add' | 'update' | 'delete-confirmation' | 'none'
  const [enableAddContent] = useState(false);
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

  // OPEN dialog for Delete (confirmation)
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

  // Search (bind input directly to grid via quickFilterText)
  const handleSearchChange = (value) => {
    setSearchValue(value);
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
              New
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
              // 🔎 Global search across all columns
              quickFilterText={searchValue}
              getQuickFilterText={(params) => {
                const r = params.data || {};
                const yesNo = r.sendToBothInd ? 'yes true 1' : 'no false 0';
                return [
                  r.clientId,
                  r.reportClientId,
                  yesNo,
                  r.description,
                ]
                  .filter(Boolean)
                  .join(' ');
              }}
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
        onDeleteConfirm={handleDialogDeleteConfirm}
        mode={dialogMode}
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
import { Dialog, DialogTitle, DialogContent, DialogActions, Button, IconButton, Divider } from '@mui/material';
import { CloseIcon } from '../../../assets/brand/svg-constants';
import { CFormInput, CFormSelect, CFormCheck, CFormTextarea } from '@coreui/react';
import CustomSnackbar from '../../../components/CustomSnackbar';
import '../../../scss/ClientReportMapping.scss';

const ClientReportMappingDialog = ({
  open,
  onClose,
  selectedRecord,             // { clientId, reportClientId, sendToBothInd, application, description } | null
  onSuccess,                   // (type: 'add'|'update', payload) => void
  onDeleteConfirm,             // () => void (only used in 'delete' mode)
  applicationOptions = [],     // [{label, value}, ...]
  mode = 'addEdit',            // 'addEdit' | 'delete'
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
      const f = {
        clientId: selectedRecord.clientId || '',
        reportClientId: selectedRecord.reportClientId || '',
        sendToBothInd: !!selectedRecord.sendToBothInd,
        application: selectedRecord.application || '',
        description: selectedRecord.description || '',
      };
      setForm(f);
      setOriginal(f);
    } else {
      setForm({ clientId: '', reportClientId: '', sendToBothInd: false, application: '', description: '' });
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
    if (!form.clientId || form.clientId.length !== 4) return false;
    if (!form.reportClientId || form.reportClientId.length !== 4) return false;
    if (!form.description.trim()) return false;
    if (!isAdd) return JSON.stringify(form) !== JSON.stringify(original);
    return true;
  };

  const handleSave = () => {
    if (form.clientId === form.reportClientId && form.sendToBothInd) {
      setSnackbarType('info');
      return;
    }
    onSuccess?.(isAdd ? 'add' : 'update', { ...form });
  };

  const handleSnackbarCancel = () => setSnackbarType('none');

  // Title varies by mode
  const title =
    mode === 'delete' ? 'Confirm Delete'
    : isAdd ? 'Add Client Report Mapping'
    : 'Edit Client Report Mapping';

  return (
    <>
      <Dialog
        open={open}
        onClose={handleClose}
        PaperProps={{
          className: 'client-report-mapping-dialog',
          sx: {
            width: 520,
            height: mode === 'delete' ? 260 : '50vh',
            maxHeight: '50vh',
            maxWidth: 'calc(100vw - 40px)',
            borderRadius: 2,
            overflow: 'hidden',
          },
        }}
      >
        <DialogTitle sx={{ pr: 6, py: 1.25, bgcolor: '#0d6efd', color:'white' }}>
          {title}
          <IconButton
            aria-label="close"
            onClick={handleClose}
            size="small"
            sx={{ position: 'absolute', right: 8, top: 8 }}
          >
            <CloseIcon />
          </IconButton>
        </DialogTitle>

        <Divider />

        {mode === 'delete' ? (
          <DialogContent dividers sx={{ p: 2 }}>
            <div style={{ lineHeight: 1.6 }}>
              Are you sure you want to delete this mapping?
              <br />
              <strong>Client:</strong> {form.clientId || '(n/a)'} &nbsp; | &nbsp;
              <strong>Report Client:</strong> {form.reportClientId || '(n/a)'}
              <br />
              <strong>Description:</strong> {form.description || '(n/a)'}
            </div>
          </DialogContent>
        ) : (
          <DialogContent dividers sx={{ p: 2 }}>
            <div className="crm-dialog-form">
              {/* Row 1 */}
              <div className="crm-row">
                <div className="crm-field">
                  <label className="crm-label">Client ID</label>
                  <CFormInput
                    type="text"
                    maxLength={4}
                    value={form.clientId}
                    onChange={(e) => handleChange('clientId', e.target.value)}
                    className="crm-input"
                    disabled={!isAdd} /* lock ids during edit */
                  />
                </div>
                <div className="crm-field">
                  <label className="crm-label">Report Client ID</label>
                  <CFormInput
                    type="text"
                    maxLength={4}
                    value={form.reportClientId}
                    onChange={(e) => handleChange('reportClientId', e.target.value)}
                    className="crm-input"
                    disabled={!isAdd}
                  />
                </div>
              </div>

              {/* Row 2 */}
              <div className="crm-row">
                <div className="crm-field">
                  <label className="crm-label">Application</label>
                  <CFormSelect
                    className="crm-input"
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

                <div className="crm-field crm-toggle">
                  <CFormCheck
                    className="crm-checkbox"
                    checked={form.sendToBothInd}
                    onChange={(e) => handleChange('sendToBothInd', e.target.checked)}
                  />
                  <span className="crm-toggle-label">Send to Both</span>
                </div>
              </div>

              {/* Row 3 */}
              <div className="crm-row">
                <div className="crm-field crm-field-full">
                  <label className="crm-label">Description</label>
                  <CFormTextarea
                    className="crm-textarea"
                    rows={3}
                    value={form.description}
                    onChange={(e) => handleChange('description', e.target.value)}
                  />
                </div>
              </div>
            </div>
          </DialogContent>
        )}

        <DialogActions sx={{ px: 2, py: 1.25 }}>
          <div className="client-report-mapping-button-container">
            <Button variant="outlined" size="small" onClick={handleClose}>
              Cancel
            </Button>
            {mode === 'delete' ? (
              <Button
                variant="contained"
                size="small"
                color="error"
                onClick={onDeleteConfirm}
              >
                Delete
              </Button>
            ) : (
              <Button variant="contained" size="small" disabled={!canSave()} onClick={handleSave}>
                Save
              </Button>
            )}
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



