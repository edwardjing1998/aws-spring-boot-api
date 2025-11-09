import React, { useEffect, useMemo, useState } from 'react';
import { Typography, Button, IconButton, Tooltip } from '@mui/material';
import InfoOutlinedIcon from '@mui/icons-material/InfoOutlined';
import EditOutlinedIcon from '@mui/icons-material/EditOutlined';
import DeleteOutlineIcon from '@mui/icons-material/DeleteOutline';
import { CCard, CCardBody } from '@coreui/react';

import ClientReportWindow from './utils/ClientReportWindow';

const PAGE_SIZE = 10;

const EditClientReport = ({ selectedGroupRow, isEditable, onDataChange }) => {
  const [tableData, setTableData] = useState([]);
  const [page, setPage] = useState(0);

  // modal for detail/edit/new/delete
  const [modalOpen, setModalOpen] = useState(false);
  const [modalMode, setModalMode] = useState('detail'); // 'detail' | 'edit' | 'new' | 'delete'
  const [modalRowIdx, setModalRowIdx] = useState(null); // null => creating new
  const [draftRow, setDraftRow] = useState(null);       // holds the "New" row or snapshot after delete

  // pagination
  const pageCount = useMemo(() => Math.ceil((tableData?.length || 0) / PAGE_SIZE), [tableData]);
  const pageData = useMemo(
    () => tableData.slice(page * PAGE_SIZE, (page + 1) * PAGE_SIZE),
    [tableData, page]
  );

  // ===== helpers to talk to parent ==================================================
  // Convert table row -> parent reportOptions item
  const rowToOption = (r) => ({
    reportDetails: { queryName: r.reportName ?? '', reportId: r.reportId ?? null },
    reportId: r.reportId ?? null,
    receiveFlag: r.receive === '1',
    outputTypeCd: Number(r.destination ?? 0),
    fileTypeCd: Number(r.fileText ?? 0),
    emailFlag: Number(r.email ?? 0),
    reportPasswordTx: r.password ?? '',
    emailBodyTx: r.emailBodyTx ?? '',
  });

  const pushUp = (rows) => {
    if (typeof onDataChange !== 'function') return;
    const next = {
      ...(selectedGroupRow ?? {}),
      reportOptions: rows.map(rowToOption),
    };
    onDataChange(next);
  };
  // ================================================================================

  // load/normalize incoming data
  useEffect(() => {
    const options = Array.isArray(selectedGroupRow?.reportOptions)
      ? selectedGroupRow.reportOptions
      : [];

    const clientData = options.map((option) => ({
      reportName: option?.reportDetails?.queryName || '',
      reportId: option?.reportDetails?.reportId ?? option?.reportId ?? null,
      receive: option?.receiveFlag ? '1' : '2', // 1=Yes, 2=No
      destination: option?.outputTypeCd !== undefined ? String(option.outputTypeCd) : '0', // 0=None,1=File,2=Print
      fileText: option?.fileTypeCd !== undefined ? String(option.fileTypeCd) : '0',       // 0=None,1=Text,2=Excel
      email: option?.emailFlag === 1 ? '1' : option?.emailFlag === 2 ? '2' : '0',        // 0=None,1=Email,2=Web
      password: option?.reportPasswordTx || '',
      emailBodyTx: option?.emailBodyTx || '',
    }));

    setTableData(clientData);
    const lastPage = Math.max(Math.ceil(clientData.length / PAGE_SIZE) - 1, 0);
    setPage((p) => Math.min(p, lastPage));
  }, [selectedGroupRow]);

  // styles
  const rowCellStyle = {
    backgroundColor: 'white',
    fontSize: '0.72rem',
    lineHeight: 1.1,
    padding: '2px 6px',
    borderBottom: '1px dotted #ddd',
    display: 'flex',
    alignItems: 'center',
    whiteSpace: 'nowrap',
    overflow: 'hidden',
    textOverflow: 'ellipsis',
  };
  const headerCellStyle = {
    backgroundColor: '#f0f0f0',
    fontWeight: 'bold',
    fontSize: '0.75rem',
    padding: '2px 6px',
    borderBottom: '1px dotted #ccc',
  };
  const labelCell = (text, align = 'center') => (
    <div style={{ ...rowCellStyle, justifyContent: align }}>
      <Typography noWrap sx={{ fontSize: '0.72rem', lineHeight: 1.1 }}>
        {text}
      </Typography>
    </div>
  );

  // label mappers
  const mapReceive = (v) => (v === '1' ? 'Yes' : v === '2' ? 'No' : 'None');
  const mapDestination = (v) => (v === '1' ? 'File' : v === '2' ? 'Print' : 'None');

  // actions -> detail/edit/delete
  const openDetail = (rowIdx) => {
    setModalMode('detail');
    setModalRowIdx(rowIdx);
    setDraftRow(null);
    setModalOpen(true);
  };
  const openEdit = (rowIdx) => {
    setModalMode('edit');
    setModalRowIdx(rowIdx);
    setDraftRow(null);
    setModalOpen(true);
  };
  const openDelete = (rowIdx) => {
    setModalMode('delete');
    setModalRowIdx(rowIdx);
    setDraftRow(null);
    setModalOpen(true);
  };

  // actual deletion logic (parent table update) — KEEP DIALOG OPEN
  const handleRemove = (rowIdx) => {
    setTableData((prev) => {
      const next = prev.filter((_, i) => i !== rowIdx);
      const lastPage = Math.max(Math.ceil(next.length / PAGE_SIZE) - 1, 0);
      setPage((p) => Math.min(p, lastPage));
      // bubble to parent
      pushUp(next);
      return next;
    });
  };

  // "New" button -> open modal in new mode with blank row
  const handleNewClick = () => {
    if (!isEditable) return;
    setModalMode('new');
    setModalRowIdx(null); // null signals "create"
    setDraftRow({
      reportName: '',
      reportId: null,
      receive: '0',
      destination: '0',
      fileText: '0',
      email: '0',
      password: '',
      emailBodyTx: ''
    });
    setModalOpen(true);
  };

  // save/create from modal
  const handleSaveFromModal = (updatedRow) => {
    if (modalRowIdx == null) {
      // creating a new row
      const normalized = {
        reportName: updatedRow?.reportName ?? '',
        reportId: updatedRow?.reportId ?? null,
        receive: updatedRow?.receive != null ? String(updatedRow.receive) : '0',
        destination: updatedRow?.destination != null ? String(updatedRow.destination) : '0',
        fileText: updatedRow?.fileText != null ? String(updatedRow.fileText) : '0',
        email: updatedRow?.email != null ? String(updatedRow.email) : '0',
        password: updatedRow?.password ?? '',
        emailBodyTx: updatedRow?.emailBodyTx ?? '',
      };
      setTableData((prev) => {
        const next = [...prev, normalized];
        const lastPage = Math.max(Math.ceil(next.length / PAGE_SIZE) - 1, 0);
        setPage(lastPage);
        // bubble to parent
        pushUp(next);
        return next;
      });
      // keep dialog open
    } else {
      // updating existing row
      setTableData((prev) => {
        const next = [...prev];
        next[modalRowIdx] = {
          ...next[modalRowIdx],
          ...updatedRow,
          reportId: updatedRow?.reportId ?? next[modalRowIdx].reportId ?? null,
        };
        // bubble to parent
        pushUp(next);
        return next;
      });
      // keep dialog open
    }
  };

  // delete confirm from modal (called after successful DELETE)
  const handleDeleteFromModal = () => {
    if (modalRowIdx == null) return;

    // snapshot the row being shown so dialog content stays visible after removal
    const snapshot = tableData[modalRowIdx] || null;
    setDraftRow(snapshot);
    setModalRowIdx(null); // so currentRow resolves to draftRow after removal

    // update table (do NOT close dialog)
    handleRemove(modalRowIdx);
  };

  const currentRow = modalRowIdx == null ? draftRow : tableData[modalRowIdx] || null;

  // Try to derive a clientId to pass down; adjust keys to your actual data shape
  const clientId =
    selectedGroupRow?.client ||
    selectedGroupRow?.clientId ||
    selectedGroupRow?.billingSp ||
    '';

  return (
    <div style={{ padding: '10px' }}>
      <CCard>
        <CCardBody>
          {/* pagination (top) */}
          <div
            style={{
              marginBottom: '6px',
              display: 'flex',
              justifyContent: 'space-between',
              alignItems: 'center',
            }}
          >
            <Button
              variant="text"
              size="small"
              sx={{ fontSize: '0.68rem', padding: '2px 6px', minWidth: 'unset', textTransform: 'none' }}
              onClick={() => setPage((p) => Math.max(p - 1, 0))}
              disabled={page === 0 || pageCount === 0}
            >
              ◀ Previous
            </Button>
            <Typography fontSize="0.72rem">
              Page {tableData.length > 0 ? page + 1 : 0} of {tableData.length > 0 ? pageCount : 0}
            </Typography>
            <Button
              variant="text"
              size="small"
              sx={{ fontSize: '0.68rem', padding: '2px 6px', minWidth: 'unset', textTransform: 'none' }}
              onClick={() => setPage((p) => Math.min(p + 1, pageCount - 1))}
              disabled={page >= pageCount - 1 || pageCount === 0}
            >
              Next ▶
            </Button>
          </div>

          {/* table */}
          <div style={{ height: '320px', marginBottom: '4px', marginTop: '2px' }}>
            <div
              style={{
                display: 'grid',
                gridTemplateColumns: '4fr 1fr 1fr 3fr',
                columnGap: '4px',
              }}
            >
              {/* headers */}
              {['Report Name', 'Receive', 'Destination', 'Action'].map((header) => (
                <div
                  key={header}
                  style={{
                    ...headerCellStyle,
                    textAlign: header === 'Action' ? 'center' : 'left',
                  }}
                >
                  {header}
                </div>
              ))}

              {/* rows */}
              {pageData.map((item, index) => {
                const rowIdx = page * PAGE_SIZE + index;
                return (
                  <React.Fragment key={`${item.reportName}-${rowIdx}`}>
                    <div style={rowCellStyle}>{item.reportName}</div>
                    {labelCell(mapReceive(item.receive))}
                    {labelCell(mapDestination(item.destination))}
                    <div style={{ ...rowCellStyle, gap: 4, justifyContent: 'center' }}>
                      <Tooltip title="Detail" arrow>
                        <IconButton
                          aria-label="detail"
                          size="small"
                          sx={{ p: 0.25, fontSize: '0.95rem' }}
                          onClick={() => openDetail(rowIdx)}
                        >
                          <InfoOutlinedIcon fontSize="inherit" />
                        </IconButton>
                      </Tooltip>
                      <Tooltip title="Edit" arrow>
                        <span>
                          <IconButton
                            aria-label="edit"
                            size="small"
                            sx={{ p: 0.25, fontSize: '0.95rem' }}
                            onClick={() => openEdit(rowIdx)}
                            disabled={!isEditable}
                          >
                            <EditOutlinedIcon fontSize="inherit" />
                          </IconButton>
                        </span>
                      </Tooltip>
                      <Tooltip title="Remove" arrow>
                        <span>
                          <IconButton
                            aria-label="remove"
                            size="small"
                            sx={{ p: 0.25, fontSize: '0.95rem' }}
                            onClick={() => openDelete(rowIdx)}
                            disabled={!isEditable}
                          >
                            <DeleteOutlineIcon fontSize="inherit" />
                          </IconButton>
                        </span>
                      </Tooltip>
                    </div>
                  </React.Fragment>
                );
              })}
            </div>
          </div>

          {/* "New" button centered horizontally */}
          <div style={{ marginTop: '8px', display: 'flex', justifyContent: 'center' }}>
            <Button
              variant="contained"
              size="small"
              onClick={handleNewClick}
              disabled={!isEditable}
            >
              New
            </Button>
          </div>
        </CCardBody>
      </CCard>

      {/* detail/edit/new/delete window */}
      <ClientReportWindow
        open={modalOpen}
        mode={modalMode}
        row={currentRow}
        clientId={clientId}
        onClose={() => {
          setModalOpen(false);
          setDraftRow(null);
        }}
        onSave={handleSaveFromModal}     // used for 'edit' and 'new'
        onDelete={handleDeleteFromModal} // used after successful DELETE (keeps dialog open)
      />
    </div>
  );
};

export default EditClientReport;




import React, { useState, useEffect } from 'react';
import { Box, IconButton, Tabs, Tab, Button, TextField, Snackbar, Alert } from '@mui/material';
import CloseIcon from '@mui/icons-material/Close';
import { CRow, CCol } from '@coreui/react';

import EditClientInformation from '../EditClientInformation';
import EditAtmCashPrefix from '../EditAtmCashPrefix';
import EditClientReport from '../EditClientReport';
import EditClientEmailSetup from '../EditClientEmailSetup';

import { handleCreate, handleUpdate, handleDelete } from './ClientService';

const ClientInformationWindow = ({
  onClose,
  selectedGroupRow,
  setSelectedGroupRow,
  mode,
  // (optional) already in your previous flow
  onClientCreated,
  onClientUpdated,
  // NEW: bubble email changes up to the page
  onClientEmailsChanged,
}) => {
  const [tabIndex, setTabIndex] = useState(0);
  const [isEditable, setIsEditable] = useState(true);
  const [status, setStatus] = useState({ open: false, severity: 'info', message: '' });

  const makeEmptyClient = () => ({
    client: '', name: '', addr: '', city: '', state: '', zip: '',
    contact: '', phone: '', active: true, faxNumber: '', billingSp: '',
    reportBreakFlag: 0, chLookUpType: 0, excludeFromReport: false, positiveReports: false,
    subClientInd: false, subClientXref: '', amexIssued: false,
    clientEmail: [], // helpful default
  });

  useEffect(() => {
    if (mode === 'new') {
      setIsEditable(true);
      setSelectedGroupRow(prev => (prev && Object.keys(prev).length ? prev : makeEmptyClient()));
    } else if (mode === 'edit') {
      setIsEditable(true);
    } else {
      setIsEditable(false);
    }
  }, [mode, setSelectedGroupRow]);

  const viewRow = mode === 'new'
    ? (selectedGroupRow ?? makeEmptyClient())
    : (selectedGroupRow ?? makeEmptyClient());

  const sharedSx = {
    '& .MuiInputBase-root': { height: '30px', fontSize: '0.78rem' },
    '& .MuiInputBase-input': { padding: '4px 4px', height: '30px', fontSize: '0.78rem', lineHeight: '1rem' },
    '& .MuiInputLabel-root': { fontSize: '0.78rem', lineHeight: '1rem' },
    '& .MuiInputBase-input.Mui-disabled': { color: 'black', WebkitTextFillColor: 'black' },
    '& .MuiInputLabel-root.Mui-disabled': { color: 'black' },
  };

  const showStatus = (message, severity = 'success') =>
    setStatus({ open: true, severity, message });
  const handleStatusClose = (_e, reason) => {
    if (reason === 'clickaway') return;
    setStatus(prev => ({ ...prev, open: false }));
  };

  const onCreateClick = async () => {
    try {
      const saved = await handleCreate(viewRow);
      showStatus('Client created successfully', 'success');
      setSelectedGroupRow(prev => ({ ...(prev ?? {}), ...(saved ?? {}) }));
      onClientCreated?.(saved);
    } catch (err) {
      console.error(err);
      showStatus(err.message || 'An error occurred while creating.', 'error');
    }
  };

  const onUpdateClick = async () => {
    try {
      const raw = await handleUpdate(viewRow);

      // ✅ Normalize: if server returns text or a "thin" object, use what the user just edited.
      const saved =
        raw && typeof raw === 'object'
          ? { ...viewRow, ...raw } // enrich "thin" payload with the current form values
          : { ...viewRow, _message: typeof raw === 'string' ? raw : undefined };

      showStatus('Client updated successfully', 'success');

      // ✅ Update the modal’s local view immediately
      setSelectedGroupRow(prev => ({ ...(prev ?? {}), ...(saved ?? {}) }));

      // ✅ Bubble to the page so the left list & preview update
      onClientUpdated?.(saved);
    } catch (err) {
      console.error(err);
      showStatus(err.message || 'An error occurred while updating.', 'error');
    }
  };

  const onDeleteClick = async () => {
    if (!viewRow?.client) {
      showStatus('Client ID is required to delete.', 'warning');
      return;
    }
    if (!window.confirm(`Delete client "${viewRow.client}"? This cannot be undone.`)) return;
    try {
      await handleDelete(viewRow.client);
      showStatus('Client deleted successfully', 'success');
    } catch (err) {
      console.error(err);
      showStatus(err.message || 'An error occurred while deleting.', 'error');
    }
  };

  const onPrimaryClick = () => {
    if (mode === 'edit') return onUpdateClick();
    if (mode === 'delete') return onDeleteClick();
    return onCreateClick();
  };

  // NEW: when child changes emails, sync local state & bubble up
  const handleEmailsChanged = (clientId, nextEmailList) => {
    if (!clientId) return;
    setSelectedGroupRow(prev => ({ ...(prev ?? {}), client: clientId, clientEmail: Array.isArray(nextEmailList) ? nextEmailList : [] }));
    onClientEmailsChanged?.(clientId, nextEmailList);
  };

  const renderButtonRow = () => (
    <CRow className="mt-3" style={{ maxWidth: 900, margin: '0 auto' }}>
      <CCol style={{ display: 'flex', justifyContent: 'flex-start' }}>
        <Button variant="outlined" size="small" onClick={() => setTabIndex((p) => Math.max(p - 1, 0))} disabled={tabIndex === 0}>
          Back
        </Button>
      </CCol>
      <CCol style={{ display: 'flex', justifyContent: 'flex-end', gap: '12px' }}>
        <Button variant="contained" size="small" color={mode === 'delete' ? 'error' : 'primary'} onClick={onPrimaryClick}>
          {mode === 'new' ? 'Create' : mode === 'edit' ? 'Update' : mode === 'delete' ? 'Delete' : 'Save'}
        </Button>
        <Button variant="outlined" size="small" onClick={() => setTabIndex((p) => Math.min(p + 1, 3))} disabled={tabIndex === 3}>
          Next
        </Button>
      </CCol>
    </CRow>
  );

  const borderPanelStyle = { border: '1px solid #ccc', borderRadius: '4px', padding: '8px', height: '440px', maxWidth: '900px', width: '100%', margin: '0 auto' };
  const noBorderPanelStyle = { border: '0px solid transparent', borderRadius: '4px', padding: '8px', height: '440px', maxWidth: '900px', width: '100%', margin: '0 auto' };

  return (
    <>
      <Box sx={{ display: 'flex', justifyContent: 'space-between', alignItems: 'center', backgroundColor: '#1976d2', color: 'white', height: 40, mx: -2, mt: -2, px: 0, py: 0, borderTopLeftRadius: 8, borderTopRightRadius: 8 }}>
        <Box sx={{ fontWeight: 600, fontSize: '0.95rem', lineHeight: '40px', pl: 2 }}>Client Information</Box>
        <IconButton onClick={onClose} size="small" sx={{ color: 'white', mr: 1 }}>
          <CloseIcon fontSize="small" />
        </IconButton>
      </Box>

      <Box sx={{ p: 2, height: 710, overflow: 'hidden', display: 'flex', flexDirection: 'column' }}>
        {/* Header inline inputs */}
        <Box sx={{ pb: 1, maxWidth: 900, mx: 'auto', width: '100%' }}>
          <CRow style={{ marginBottom: '12px', marginTop: 0 }}>
            <CCol xs="6">
              <Box sx={{ display: 'flex', alignItems: 'center', gap: 1 }}>
                <Box component="label" sx={{ fontSize: '0.78rem', minWidth: 72 }}>Client ID</Box>
                <TextField
                  label=""
                  value={viewRow.client ?? ''}
                  size="small"
                  disabled={!isEditable}
                  sx={{ ...sharedSx, minWidth: 160, flex: 1 }}
                  onChange={(e) =>
                    setSelectedGroupRow(prev => ({ ...(prev ?? makeEmptyClient()), client: e.target.value }))
                  }
                />
              </Box>
            </CCol>
            <CCol xs="6">
              <Box sx={{ display: 'flex', alignItems: 'center', gap: 1 }}>
                <Box component="label" sx={{ fontSize: '0.78rem', minWidth: 72 }}>Name</Box>
                <TextField
                  label=""
                  value={viewRow.name ?? ''}
                  size="small"
                  disabled={!isEditable}
                  sx={{ ...sharedSx, flex: 1 }}
                  onChange={(e) =>
                    setSelectedGroupRow(prev => ({ ...(prev ?? makeEmptyClient()), name: e.target.value }))
                  }
                />
              </Box>
            </CCol>
          </CRow>
        </Box>

        <Tabs value={tabIndex} onChange={(_e, v) => setTabIndex(v)} variant="fullWidth" sx={{ mt: -0.5, mb: 2 }} TabIndicatorProps={{ sx: { width: '30px', left: 'calc(50% - 15px)' } }}>
          <Tab label={<Box sx={{ display: 'flex', alignItems: 'center', gap: .5 }}><Box sx={{ width:18, height:18, borderRadius:'50%', backgroundColor:'#1976d2', color:'white', fontSize:'.7rem', display:'flex', alignItems:'center', justifyContent:'center' }}>1</Box>Client Information</Box>} sx={{ fontSize: '0.78rem', textTransform: 'none', minWidth: 205, maxWidth: 205, px: 1 }} />
          <Tab label={<Box sx={{ display: 'flex', alignItems: 'center', gap: .5 }}><Box sx={{ width:18, height:18, borderRadius:'50%', backgroundColor:'#1976d2', color:'white', fontSize:'.7rem', display:'flex', alignItems:'center', justifyContent:'center' }}>2</Box>Client Email Setup</Box>} sx={{ fontSize: '0.78rem', textTransform: 'none', minWidth: 160, maxWidth: 170, px: 1 }} />
          <Tab label={<Box sx={{ display: 'flex', alignItems: 'center', gap: .5 }}><Box sx={{ width:18, height:18, borderRadius:'50%', backgroundColor:'#1976d2', color:'white', fontSize:'.7rem', display:'flex', alignItems:'center', justifyContent:'center' }}>3</Box>Client Reports</Box>} sx={{ fontSize: '0.78rem', textTransform: 'none', minWidth: 135, maxWidth: 145, px: 1 }} />
          <Tab label={<Box sx={{ display: 'flex', alignItems: 'center', gap: .5 }}><Box sx={{ width:18, height:18, borderRadius:'50%', backgroundColor:'#1976d2', color:'white', fontSize:'.7rem', display:'flex', alignItems:'center', justifyContent:'center' }}>4</Box>Client ATM/Cash Prefixes</Box>} sx={{ fontSize: '0.78rem', textTransform: 'none', minWidth: 195, maxWidth: 205, px: 1 }} />
        </Tabs>

        <Box sx={{ flex: 1, overflow: 'auto' }}>
          {tabIndex === 0 && (
            <>
              <CRow className="mb-2" style={borderPanelStyle}>
                <CCol>
                  <div style={{ fontSize: '0.78rem', paddingTop: '12px', height: '100%' }}>
                    <EditClientInformation
                      selectedGroupRow={viewRow}
                      isEditable={isEditable}
                      setSelectedGroupRow={setSelectedGroupRow}
                      mode={mode}
                      onClientUpdated={onClientUpdated}
                    />
                  </div>
                </CCol>
              </CRow>
              {renderButtonRow()}
            </>
          )}

          {tabIndex === 1 && (
            <>
              <CRow className="mb-2" style={noBorderPanelStyle}>
                <CCol>
                  <div style={{ fontSize: '0.78rem', paddingTop: '12px', height: '100%' }}>
                    <EditClientEmailSetup
                      selectedGroupRow={viewRow}
                      isEditable={isEditable}
                      // NEW: pass handler down
                      onEmailsChanged={handleEmailsChanged}
                    />
                  </div>
                </CCol>
              </CRow>
              {renderButtonRow()}
            </>
          )}

          {tabIndex === 2 && (
            <>
              <CRow className="mb-2" style={noBorderPanelStyle}>
                <CCol>
                  <div style={{ fontSize: '0.78rem', paddingTop: '12px', height: '100%' }}>
                    <EditClientReport selectedGroupRow={viewRow} isEditable={isEditable} 
                      onDataChange={(nextSelectedGroupRow) => {
                             // keep parent in sync so the whole window reflects changes
                             setSelectedGroupRow(nextSelectedGroupRow);
                       }} />
                  </div>
                </CCol>
              </CRow>
              {renderButtonRow()}
            </>
          )}

          {tabIndex === 3 && (
            <>
              <CRow className="mb-2" style={borderPanelStyle}>
                <CCol>
                  <div style={{ fontSize: '0.78rem', paddingTop: '12px', height: '80%' }}>
                    <EditAtmCashPrefix selectedGroupRow={viewRow} isEditable={isEditable} />
                  </div>
                </CCol>
              </CRow>
              {renderButtonRow()}
            </>
          )}
        </Box>
      </Box>

      <Snackbar open={status.open} autoHideDuration={3000} onClose={handleStatusClose} anchorOrigin={{ vertical: 'top', horizontal: 'center' }}>
        <Alert onClose={handleStatusClose} severity={status.severity} variant="filled" sx={{ width: '100%' }}>
          {status.message}
        </Alert>
      </Snackbar>
    </>
  );
};

export default ClientInformationWindow;





// ClientReportWindow.jsx
import React, { useMemo, useState, useEffect } from 'react';
import {
  Dialog, DialogTitle, DialogContent, DialogActions,
  Button, Box, TextField, FormControl, Select, MenuItem, Typography, Alert,
} from '@mui/material';

// Autocomplete for report selection in "new" mode
import ClientReportAutoCompleteInputBox from '../../../../components/ClientReportAutoCompleteInputBox';

/** Centralized option maps (code -> label) */
const RECEIVE_OPTIONS = [
  { value: '0', label: 'None' },
  { value: '1', label: 'Yes' },
  { value: '2', label: 'No'  },
];
const DESTINATION_OPTIONS = [
  { value: '0', label: 'None' },
  { value: '1', label: 'File' },
  { value: '2', label: 'Print' },
];
const FILETYPE_OPTIONS = [
  { value: '0', label: 'None'  },
  { value: '1', label: 'Text'  },
  { value: '2', label: 'Excel' },
];
const EMAIL_OPTIONS = [
  { value: '0', label: 'None'  },
  { value: '1', label: 'Email' },
  { value: '2', label: 'Web'   },
];

const labelFor = (options, v) => (options.find(o => o.value === String(v))?.label ?? 'None');

// --- helpers ----
const extractReportId = (val, fallback) => {
  if (val == null) return fallback ?? null;
  if (typeof val === 'number') return val;
  const s = String(val).trim();
  const m = s.match(/^(\d+)/);
  return m ? Number(m[1]) : (fallback ?? null);
};
const toBool = (code) => String(code) === '1';
const toInt = (code, def = 0) => {
  const n = Number(code);
  return Number.isFinite(n) ? n : def;
};

/**
 * Props:
 * - open: boolean
 * - mode: 'detail' | 'edit' | 'new' | 'delete'
 * - row: { reportName, reportId?, receive, destination, fileText, email, password, emailBodyTx } | undefined
 * - clientId: string
 * - onClose: () => void
 * - onSave: (updatedRow) => void           // called after successful POST for 'edit' and 'new'
 * - onDelete?: (rowToDelete) => void       // called after successful DELETE
 */
const ClientReportWindow = ({
  open,
  mode = 'detail',
  row,
  clientId,
  onClose,
  onSave,
  onDelete,
}) => {
  const EMPTY_ROW = {
    reportName: '',
    reportId: null,
    receive: '0',
    destination: '0',
    fileText: '0',
    email: '0',
    password: '',
    emailBodyTx: '',
  };

  const normalize = (r) => {
    const reportName = r?.reportName ?? '';
    const reportId = r?.reportId != null
      ? Number(r.reportId)
      : extractReportId(reportName, null);
    return {
      reportName,
      reportId,
      receive: r?.receive != null ? String(r.receive) : '0',
      destination: r?.destination != null ? String(r.destination) : '0',
      fileText: r?.fileText != null ? String(r.fileText) : '0',
      email: r?.email != null ? String(r.email) : '0',
      password: r?.password ?? '',
      emailBodyTx: r?.emailBodyTx ?? '',
    };
  };

  const effectiveSource = mode === 'new' ? EMPTY_ROW : (row ?? EMPTY_ROW);

  const [form, setForm] = useState(normalize(effectiveSource));
  useEffect(() => {
    setForm(normalize(mode === 'new' ? EMPTY_ROW : (row ?? EMPTY_ROW)));
  }, [row, mode]);

  // local state for "new" autocomplete
  const [reportNameInput, setReportNameInput] = useState(form.reportName || '');
  const [isWildcardMode, setIsWildcardMode] = useState(false);

  useEffect(() => {
    if (mode === 'new') {
      const rid = extractReportId(reportNameInput, null);
      setForm((prev) => ({ ...prev, reportName: reportNameInput || '', reportId: rid }));
    }
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, [reportNameInput, mode]);

  const isReadOnly = mode === 'detail' || mode === 'delete';
  const isEditable = mode === 'edit' || mode === 'new';

  const cellSelectSx = {
    fontSize: '0.85rem',
    backgroundColor: 'white',
    '& .MuiSelect-select': { padding: '6px 10px', fontSize: '0.85rem' },
  };
  const labelSx = { fontSize: '0.8rem', color: '#666', mb: 0.5 };

  const handleChange = (key) => (e) =>
    setForm((prev) => ({ ...prev, [key]: String(e.target.value) }));

  const ready = useMemo(() => !!form, [form]);

  const renderLabel = (options, value) => (
    <Typography variant="body2" sx={{ fontSize: '0.9rem' }}>
      {labelFor(options, value)}
    </Typography>
  );
  const renderSelect = (options, value, key) => (
    <FormControl fullWidth size="small">
      <Select value={value} onChange={handleChange(key)} sx={cellSelectSx} disabled={!isEditable}>
        {options.map(opt => (
          <MenuItem key={opt.value} value={opt.value} sx={{ fontSize: '0.85rem' }}>
            {opt.label}
          </MenuItem>
        ))}
      </Select>
    </FormControl>
  );

  const titleText = (() => {
    switch (mode) {
      case 'edit': return 'Edit Client Report';
      case 'new': return 'New Client Report';
      case 'delete': return 'Delete Client Report';
      default: return 'Report Details';
    }
  })();

  // --- status + API integration ---
  const [submitting, setSubmitting] = useState(false);
  const [statusMessage, setStatusMessage] = useState(null); // { severity: 'success'|'error', text: string }

  // clear status when mode changes or dialog reopens
  useEffect(() => { setStatusMessage(null); }, [mode, open]);

  const buildPayload = () => ({
    clientId: clientId ?? '',
    reportId: form.reportId ?? extractReportId(form.reportName, null),
    receiveFlag: toBool(form.receive),
    outputTypeCd: toInt(form.destination, 0),
    fileTypeCd: toInt(form.fileText, 0),
    emailFlag: toInt(form.email, 0),
    emailBodyTx: form.emailBodyTx ?? null,
    reportPasswordTx: form.password || '',
  });

  const postJson = async (url, body) => {
    const res = await fetch(url, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json', accept: '*/*' },
      body: JSON.stringify(body),
    });
    if (!res.ok) {
      const text = await res.text().catch(() => '');
      throw new Error(`${res.status} ${res.statusText}${text ? ` - ${text}` : ''}`);
    }
    try { return await res.json(); } catch { return null; }
  };

  const deleteCall = async (url) => {
    const res = await fetch(url, {
      method: 'DELETE',
      headers: { accept: '*/*' },
    });
    if (!res.ok) {
      const text = await res.text().catch(() => '');
      throw new Error(`${res.status} ${res.statusText}${text ? ` - ${text}` : ''}`);
    }
    return null;
  };

  const handlePrimaryAction = async () => {
    const rid = form.reportId ?? extractReportId(form.reportName, null);

    if (mode === 'delete') {
      if (!clientId) {
        setStatusMessage({ severity: 'error', text: 'Missing clientId. Cannot delete.' });
        return;
      }
      if (!rid) {
        setStatusMessage({ severity: 'error', text: 'Missing reportId. Cannot delete.' });
        return;
      }

      try {
        setSubmitting(true);
        const url = `http://localhost:8089/client-sysprin-writer/api/client/reportOptions/delete/${encodeURIComponent(clientId)}/${encodeURIComponent(rid)}`;
        await deleteCall(url);
        setStatusMessage({ severity: 'success', text: 'Report deleted successfully.' });
        onDelete?.(form); // parent updates table but does NOT close dialog
      } catch (err) {
        console.error('Delete failed:', err);
        setStatusMessage({ severity: 'error', text: `Delete failed: ${err.message}` });
      } finally {
        setSubmitting(false);
      }
      return;
    }

    // Create / Update
    if (!clientId) {
      setStatusMessage({ severity: 'error', text: 'Missing clientId. Please pass clientId to the window.' });
      return;
    }
    if (mode === 'new' && (!rid || !form.reportName)) {
      setStatusMessage({ severity: 'error', text: 'Please select a report first.' });
      return;
    }
    if (mode === 'edit' && !rid) {
      setStatusMessage({ severity: 'error', text: 'Missing reportId. Please pick a valid report.' });
      return;
    }

    const payload = buildPayload();

    try {
      setSubmitting(true);
      if (mode === 'new') {
        await postJson(
          'http://localhost:8089/client-sysprin-writer/api/client/reportOptions/Initiate',
          payload
        );
        onSave?.({
          ...form,
          reportId: form.reportId,
          receive: form.receive,
          destination: form.destination,
          fileText: form.fileText,
          email: form.email,
          password: form.password,
          emailBodyTx: form.emailBodyTx,
        });
        setStatusMessage({ severity: 'success', text: 'Report created successfully.' });
      } else if (mode === 'edit') {
        await postJson(
          'http://localhost:8089/client-sysprin-writer/api/client/reportOptions/update',
          payload
        );
        onSave?.(form);
        setStatusMessage({ severity: 'success', text: 'Report updated successfully.' });
      }
    } catch (err) {
      console.error('Submit failed:', err);
      setStatusMessage({ severity: 'error', text: `Submit failed: ${err.message}` });
    } finally {
      setSubmitting(false);
    }
  };

  const primaryDisabled =
    submitting ||
    (mode === 'new' && (!form.reportName || !form.reportId));

  return (
    <Dialog
      open={open}
      onClose={onClose}
      maxWidth="sm"
      fullWidth
    >
      <DialogTitle
        sx={{
          bgcolor: '#1976d2',
          color: '#fff',
          py: 0.5,
          px: 1.5,
          minHeight: 'unset',
          display: 'flex',
          alignItems: 'center',
        }}
      >
        <Typography component="span" sx={{ fontSize: '0.9rem', lineHeight: 2, fontWeight: 400, color: 'inherit', mb: 0 }}>
          {titleText}
        </Typography>
      </DialogTitle>

      <DialogContent dividers>
        {/* Status message (success / error) */}
        {statusMessage && (
          <Box gridColumn="1 / span 2" sx={{ mb: 1 }}>
            <Alert
              severity={statusMessage.severity}
              onClose={() => setStatusMessage(null)}
              sx={{ fontSize: '0.85rem' }}
            >
              {statusMessage.text}
            </Alert>
          </Box>
        )}

        {!ready ? (
          <Typography variant="body2">Loading…</Typography>
        ) : (
          <Box display="grid" gridTemplateColumns="1fr 1fr" gap={2} mt={0.5}>
            {/* Report Name (left) */}
            <Box>
              <Typography sx={labelSx}>Report Name</Typography>
              {mode === 'new' ? (
                <ClientReportAutoCompleteInputBox
                  inputValue={reportNameInput}
                  setInputValue={setReportNameInput}
                  onClientsFetched={() => {}}
                  isWildcardMode={isWildcardMode}
                  setIsWildcardMode={setIsWildcardMode}
                  sx={{
                    '& .MuiOutlinedInput-root': {
                      '& fieldset': { border: '1px solid rgba(0,0,0,0.23)' },
                    },
                  }}
                />
              ) : isEditable ? (
                <TextField
                  value={form.reportName}
                  onChange={(e) => {
                    const v = e.target.value;
                    setForm((prev) => ({ ...prev, reportName: v, reportId: extractReportId(v, prev.reportId) }));
                  }}
                  size="small"
                  fullWidth
                />
              ) : (
                <Typography variant="body2" sx={{ fontSize: '0.9rem' }}>
                  {form.reportName || '(empty)'}
                </Typography>
              )}
            </Box>

            {/* Password (right, same row) */}
            <Box>
              <Typography sx={labelSx}>Password</Typography>
              {isEditable ? (
                <TextField
                  type="password"
                  value={form.password}
                  onChange={(e) => setForm((p) => ({ ...p, password: e.target.value }))}
                  size="small"
                  fullWidth
                />
              ) : (
                <Typography variant="body2" sx={{ fontSize: '0.9rem' }}>
                  {form.password ? '••••••••' : '(empty)'}
                </Typography>
              )}
            </Box>

            {/* Receive */}
            <Box>
              <Typography sx={labelSx}>Receive</Typography>
              {isEditable ? renderSelect(RECEIVE_OPTIONS, form.receive, 'receive') : renderLabel(RECEIVE_OPTIONS, form.receive)}
            </Box>

            {/* Destination */}
            <Box>
              <Typography sx={labelSx}>Destination</Typography>
              {isEditable ? renderSelect(DESTINATION_OPTIONS, form.destination, 'destination') : renderLabel(DESTINATION_OPTIONS, form.destination)}
            </Box>

            {/* File Type */}
            <Box>
              <Typography sx={labelSx}>File Type</Typography>
              {isEditable ? renderSelect(FILETYPE_OPTIONS, form.fileText, 'fileText') : renderLabel(FILETYPE_OPTIONS, form.fileText)}
            </Box>

            {/* Email */}
            <Box>
              <Typography sx={labelSx}>Email</Typography>
              {isEditable ? renderSelect(EMAIL_OPTIONS, form.email, 'email') : renderLabel(EMAIL_OPTIONS, form.email)}
            </Box>

            <Box gridColumn="1 / span 2">
            <Typography sx={labelSx}>Email Body</Typography>
            {isEditable ? (
                <TextField
                    value={form.emailBodyTx}
                    onChange={(e) => setForm((p) => ({ ...p, emailBodyTx: e.target.value }))}
                    size="small"
                    fullWidth
                    multiline
                    rows={3}                          // base height
                    inputProps={{ maxLength: 500 }}
                    helperText={`${(form.emailBodyTx?.length ?? 0)}/500`}
                    sx={{
                        '& .MuiInputBase-inputMultiline': {
                        maxHeight: 100,        // cap
                        overflowY: 'auto',
                        lineHeight: 1.35,
                        padding: '6px 8px',
                        },
                    }}
                    />
            ) : (
                <TextField
                    value={form.emailBodyTx}
                    onChange={(e) => setForm((p) => ({ ...p, emailBodyTx: e.target.value }))}
                    size="small"
                    fullWidth
                    multiline
                    rows={3}                          // base height
                    inputProps={{ maxLength: 500 }}
                    helperText={`${(form.emailBodyTx?.length ?? 0)}/500`}
                    sx={{
                        '& .MuiInputBase-inputMultiline': {
                        maxHeight: 100,        // cap
                        overflowY: 'auto',
                        lineHeight: 1.35,
                        padding: '6px 8px',
                        },
                    }}
                />
            )}
            </Box>

            {mode === 'delete' && (
              <Box gridColumn="1 / span 2" sx={{ mt: 1 }}>
                <Typography variant="body2" color="error" sx={{ fontSize: '0.9rem' }}>
                  This action cannot be undone. Do you want to permanently delete this report?
                </Typography>
              </Box>
            )}

            {!clientId && (mode === 'new' || mode === 'edit') && (
              <Box gridColumn="1 / span 2">
                <Typography variant="caption" color="error">
                  Warning: clientId is missing. The API call will fail. Pass clientId to ClientReportWindow.
                </Typography>
              </Box>
            )}
          </Box>
        )}
      </DialogContent>

      <DialogActions>
        <Button onClick={onClose} variant="outlined" size="small" disabled={submitting}>
          {mode === 'delete' ? 'Cancel' : 'Close'}
        </Button>

        {mode === 'edit' && (
          <Button onClick={handlePrimaryAction} variant="contained" size="small" disabled={submitting}>
            Save
          </Button>
        )}

        {mode === 'new' && (
          <Button onClick={handlePrimaryAction} variant="contained" size="small" disabled={primaryDisabled}>
            Create
          </Button>
        )}

        {mode === 'delete' && (
          <Button onClick={handlePrimaryAction} variant="contained" color="error" size="small" disabled={submitting}>
            Delete
          </Button>
        )}
      </DialogActions>
    </Dialog>
  );
};

export default ClientReportWindow;




