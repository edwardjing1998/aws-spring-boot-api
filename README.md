// utils/AtmCashPrefixDetailWindow.jsx
import React, { useMemo, useState, useEffect } from 'react';
import {
  Dialog,
  DialogTitle,
  DialogContent,
  DialogActions,
  Typography,
  Button,
  Box,
  Divider,
  TextField,
  FormControl,
  Select,
  MenuItem,
  Alert,
} from '@mui/material';

const atmCashLabel = (rule) => {
  if (rule === '0' || rule === 0) return 'Destroy';
  if (rule === '1' || rule === 1) return 'Return';
  return 'N/A';
};

/**
 * Props:
 * - open: boolean
 * - mode: 'detail' | 'edit' | 'delete' | 'new'
 * - row: { billingSp, prefix, atmCashRule } | null
 * - onClose: () => void
 * - onConfirm?: (mode, draftRow) => Promise<void> | void   // used for EDIT confirm only
 * - onCreated?: (createdRow) => void                       // optional hook after successful create
 * - onDeleted?: (deletedRow) => void                       // optional hook after successful delete
 */
const AtmCashPrefixDetailWindow = ({
  open,
  mode = 'detail',
  row,
  onClose,
  onConfirm,
  onCreated,
  onDeleted,
}) => {
  const title = useMemo(() => {
    if (mode === 'edit') return 'Edit Prefix';
    if (mode === 'delete') return 'Delete Prefix';
    if (mode === 'new') return 'New Prefix';
    return 'Prefix Detail';
  }, [mode]);

  // Local draft for edit/new modes, also used to send payload on delete
  const [draft, setDraft] = useState({ billingSp: '', prefix: '', atmCashRule: '' });
  const [submitting, setSubmitting] = useState(false);
  const [status, setStatus] = useState(null); // { severity, text }

  useEffect(() => {
    // reset status whenever dialog opens or mode changes
    if (open) setStatus(null);

    if (mode === 'new') {
      setDraft({
        billingSp: row?.billingSp ?? '',
        prefix: '',
        atmCashRule: '',
      });
    } else if (row) {
      setDraft({
        billingSp: row.billingSp ?? '',
        prefix: row.prefix ?? '',
        atmCashRule: String(row.atmCashRule ?? ''),
      });
    } else {
      setDraft({ billingSp: '', prefix: '', atmCashRule: '' });
    }
  }, [row, open, mode]);

  const isDetail = mode === 'detail';
  const isEdit = mode === 'edit';
  const isDelete = mode === 'delete';
  const isNew = mode === 'new';

  // CREATE inside the window and show status
  const handleCreate = async () => {
    if (!draft.prefix || draft.atmCashRule === '') {
      setStatus({ severity: 'warning', text: 'Please enter both Prefix and ATM/Cash Rule.' });
      return;
    }

    try {
      setSubmitting(true);
      setStatus({ severity: 'info', text: 'Creating…' });

      const res = await fetch('http://localhost:8089/client-sysprin-writer/api/prefix/add', {
        method: 'POST',
        headers: { accept: '*/*', 'Content-Type': 'application/json' },
        body: JSON.stringify({
          billingSp: draft.billingSp ?? '',
          prefix: draft.prefix ?? '',
          atmCashRule: String(draft.atmCashRule ?? ''),
        }),
      });

      if (!res.ok) {
        const text = await res.text().catch(() => '');
        throw new Error(`${res.status} ${res.statusText}${text ? ` - ${text}` : ''}`);
      }

      setStatus({ severity: 'success', text: 'Prefix created successfully.' });
      onCreated?.({
        billingSp: draft.billingSp ?? '',
        prefix: draft.prefix ?? '',
        atmCashRule: String(draft.atmCashRule ?? ''),
      });
      // Optionally auto-close:
      // onClose?.();
    } catch (err) {
      console.error('Create failed:', err);
      setStatus({ severity: 'error', text: `Create failed: ${err.message}` });
    } finally {
      setSubmitting(false);
    }
  };

  // DELETE inside the window and show status
  const handleDelete = async () => {
    if (!draft.prefix || draft.atmCashRule === '' || !draft.billingSp) {
      setStatus({
        severity: 'warning',
        text: 'Missing required fields. Please ensure Billing SP, Prefix, and ATM/Cash Rule are present.',
      });
      return;
    }

    try {
      setSubmitting(true);
      setStatus({ severity: 'info', text: 'Deleting…' });

      const res = await fetch('http://localhost:8089/client-sysprin-writer/api/prefix/delete', {
        method: 'DELETE',
        headers: { accept: '*/*', 'Content-Type': 'application/json' },
        body: JSON.stringify({
          billingSp: draft.billingSp ?? '',
          prefix: draft.prefix ?? '',
          atmCashRule: String(draft.atmCashRule ?? ''),
        }),
      });

      if (!res.ok) {
        const text = await res.text().catch(() => '');
        throw new Error(`${res.status} ${res.statusText}${text ? ` - ${text}` : ''}`);
      }

      setStatus({ severity: 'success', text: 'Prefix deleted successfully.' });
      onDeleted?.({
        billingSp: draft.billingSp ?? '',
        prefix: draft.prefix ?? '',
        atmCashRule: String(draft.atmCashRule ?? ''),
      });
      // Optionally auto-close:
      // onClose?.();
    } catch (err) {
      console.error('Delete failed:', err);
      setStatus({ severity: 'error', text: `Delete failed: ${err.message}` });
    } finally {
      setSubmitting(false);
    }
  };

  // EDIT (now calls REST PUT)
  const handleEditConfirm = async () => {
    if (!onConfirm) {
      // even if parent didn't pass a handler, still call API
    }
    if (!draft.billingSp || !draft.prefix || draft.atmCashRule === '') {
      setStatus({ severity: 'warning', text: 'Please fill Billing SP, Prefix, and ATM/Cash Rule.' });
      return;
    }
    try {
      setSubmitting(true);
      setStatus({ severity: 'info', text: 'Saving…' });

      const res = await fetch('http://localhost:8089/client-sysprin-writer/api/prefix/update', {
        method: 'PUT',
        headers: { accept: '*/*', 'Content-Type': 'application/json' },
        body: JSON.stringify({
          billingSp: draft.billingSp ?? '',
          prefix: draft.prefix ?? '',
          atmCashRule: String(draft.atmCashRule ?? ''),
        }),
      });

      if (!res.ok) {
        const text = await res.text().catch(() => '');
        throw new Error(`${res.status} ${res.statusText}${text ? ` - ${text}` : ''}`);
      }

      setStatus({ severity: 'success', text: 'Saved successfully.' });

      // notify parent so it can refresh local list/state
      await Promise.resolve(onConfirm?.('edit', { ...draft }));

      // keep dialog open (do not call onClose)
    } catch (err) {
      console.error('Edit failed:', err);
      setStatus({ severity: 'error', text: `Edit failed: ${err?.message ?? err}` });
    } finally {
      setSubmitting(false);
    }
  };

  return (
    <Dialog open={open} onClose={onClose} maxWidth="xs" fullWidth>
      <DialogTitle sx={{ fontSize: '0.95rem' }}>{title}</DialogTitle>
      <Divider />
      <DialogContent dividers sx={{ pt: 1 }}>
        {status && (
          <Box sx={{ mb: 1 }}>
            <Alert
              severity={status.severity}
              onClose={() => setStatus(null)}
              sx={{ fontSize: '0.85rem' }}
            >
              {status.text}
            </Alert>
          </Box>
        )}

        {!row && !isNew ? (
          <Typography sx={{ fontSize: '0.9rem' }}>(No data)</Typography>
        ) : isDetail ? (
          // DETAIL VIEW
          <Box display="grid" gridTemplateColumns="1fr 1fr" gap={1}>
            <Typography sx={{ fontSize: '0.8rem', color: '#666' }}>Billing SP</Typography>
            <Typography sx={{ fontSize: '0.9rem' }}>{row?.billingSp ?? '(empty)'}</Typography>

            <Typography sx={{ fontSize: '0.8rem', color: '#666' }}>Prefix</Typography>
            <Typography sx={{ fontSize: '0.9rem' }}>{row?.prefix ?? '(empty)'}</Typography>

            <Typography sx={{ fontSize: '0.8rem', color: '#666' }}>ATM/Cash</Typography>
            <Typography sx={{ fontSize: '0.9rem' }}>{atmCashLabel(row?.atmCashRule)}</Typography>
          </Box>
        ) : isEdit || isNew ? (
          // EDIT / NEW FORM (same UI)
          <Box display="grid" gridTemplateColumns="1fr" gap={1}>
            <div>
              <Typography sx={{ fontSize: '0.8rem', color: '#666', mb: 0.5 }}>Billing SP</Typography>
              <TextField
                size="small"
                fullWidth
                value={draft.billingSp}
                onChange={(e) => setDraft((d) => ({ ...d, billingSp: e.target.value }))}
              />
            </div>

            <div>
              <Typography sx={{ fontSize: '0.8rem', color: '#666', mb: 0.5 }}>Prefix</Typography>
              <TextField
                size="small"
                fullWidth
                value={draft.prefix}
                onChange={(e) => setDraft((d) => ({ ...d, prefix: e.target.value }))}
              />
            </div>

            <div>
              <Typography sx={{ fontSize: '0.8rem', color: '#666', mb: 0.5 }}>ATM/Cash</Typography>
              <FormControl fullWidth size="small">
                <Select
                  value={draft.atmCashRule}
                  onChange={(e) => setDraft((d) => ({ ...d, atmCashRule: e.target.value }))}
                  sx={{ '.MuiSelect-select': { fontSize: '0.9rem' } }}
                >
                  <MenuItem value=""><em>Select Rule</em></MenuItem>
                  <MenuItem value="0">Destroy</MenuItem>
                  <MenuItem value="1">Return</MenuItem>
                </Select>
              </FormControl>
            </div>
          </Box>
        ) : (
          // DELETE CONFIRM
          <Box>
            <Typography sx={{ fontSize: '0.9rem', mb: 1 }}>
              Are you sure you want to delete this prefix?
            </Typography>
            <Box display="grid" gridTemplateColumns="1fr 1fr" gap={1}>
              <Typography sx={{ fontSize: '0.8rem', color: '#666' }}>Billing SP</Typography>
              <Typography sx={{ fontSize: '0.9rem' }}>{draft.billingSp || row?.billingSp || '(empty)'}</Typography>

              <Typography sx={{ fontSize: '0.8rem', color: '#666' }}>Prefix</Typography>
              <Typography sx={{ fontSize: '0.9rem' }}>{draft.prefix || row?.prefix || '(empty)'}</Typography>

              <Typography sx={{ fontSize: '0.8rem', color: '#666' }}>ATM/Cash</Typography>
              <Typography sx={{ fontSize: '0.9rem' }}>
                {atmCashLabel(draft.atmCashRule || row?.atmCashRule)}
              </Typography>
            </Box>
          </Box>
        )}
      </DialogContent>

      <DialogActions>
        <Button
          onClick={onClose}
          variant="outlined"
          size="small"
          disabled={submitting}
          sx={{ textTransform: 'none' }}
        >
          {isDelete ? 'Cancel' : 'Close'}
        </Button>

        {isNew && (
          <Button
            onClick={handleCreate}
            variant="contained"
            size="small"
            disabled={submitting}
            sx={{ textTransform: 'none' }}
          >
            {submitting ? 'Creating…' : 'Create'}
          </Button>
        )}

        {isEdit && (
          <Button
            onClick={handleEditConfirm}
            variant="contained"
            size="small"
            disabled={submitting}
            sx={{ textTransform: 'none' }}
          >
            Save
          </Button>
        )}

        {isDelete && (
          <Button
            onClick={handleDelete}
            color="error"
            variant="contained"
            size="small"
            disabled={submitting}
            sx={{ textTransform: 'none' }}
          >
            {submitting ? 'Deleting…' : 'Delete'}
          </Button>
        )}
      </DialogActions>
    </Dialog>
  );
};

export default AtmCashPrefixDetailWindow;



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
                    <EditClientReport
                        selectedGroupRow={viewRow}
                        isEditable={isEditable}
                        onDataChange={(nextSelectedGroupRow) => {
                          setSelectedGroupRow((prev) => {
                            if (!prev) return nextSelectedGroupRow;

                            const a = prev?.reportOptions ?? [];
                            const b = nextSelectedGroupRow?.reportOptions ?? [];

                            const changed =
                              a.length !== b.length ||
                              a.some((x, i) => {
                                const y = b[i] || {};
                                const xid = x.reportDetails?.reportId ?? x.reportId ?? null;
                                const yid = y.reportDetails?.reportId ?? y.reportId ?? null;
                                return (
                                  (x.reportDetails?.queryName ?? '') !== (y.reportDetails?.queryName ?? '') ||
                                  xid !== yid ||
                                  !!x.receiveFlag !== !!y.receiveFlag ||
                                  Number(x.outputTypeCd ?? 0) !== Number(y.outputTypeCd ?? 0) ||
                                  Number(x.fileTypeCd ?? 0) !== Number(y.fileTypeCd ?? 0) ||
                                  Number(x.emailFlag ?? 0) !== Number(y.emailFlag ?? 0) ||
                                  (x.reportPasswordTx ?? '') !== (y.reportPasswordTx ?? '') ||
                                  (x.emailBodyTx ?? '') !== (y.emailBodyTx ?? '')
                                );
                              });

                            if (!changed) return prev;         // no-op: avoid loops
                            const merged = { ...prev, reportOptions: b };
                            // also bubble to the page (if it listens here)
                            onClientUpdated?.(merged);
                            return merged;
                          });
                        }}
                      />
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



// EditAtmCashPrefix.jsx
import React, { useEffect, useMemo, useState } from 'react';
import {
  Typography,
  IconButton,
  Button,
  Tooltip,
} from '@mui/material';
import { CRow, CCol } from '@coreui/react';
import VisibilityIcon from '@mui/icons-material/Visibility';
import EditIcon from '@mui/icons-material/Edit';
import DeleteIcon from '@mui/icons-material/Delete';

import AtmCashPrefixDetailWindow from './utils/AtmCashPrefixDetailWindow';

const PAGE_SIZE = 5;

const EditAtmCashPrefix = ({ selectedGroupRow = {} }) => {
  const initial = selectedGroupRow.sysPrinsPrefixes || [];
  const [prefixes, setPrefixes] = useState(initial);

  const [selectedPrefix, setSelectedPrefix] = useState('');
  const [page, setPage] = useState(0);

  // Window state (handles detail/edit/delete/new)
  const [winOpen, setWinOpen] = useState(false);
  const [winMode, setWinMode] = useState('detail'); // 'detail' | 'edit' | 'delete' | 'new'
  const [winRow, setWinRow] = useState(null);

  // keep in sync if parent switches groups
  useEffect(() => {
    setPrefixes(selectedGroupRow.sysPrinsPrefixes || []);
    setPage(0);
    setSelectedPrefix('');
  }, [selectedGroupRow]);

  const pageCount = useMemo(
    () => Math.ceil((prefixes.length || 0) / PAGE_SIZE) || 0,
    [prefixes.length]
  );
  const pageData = useMemo(
    () => prefixes.slice(page * PAGE_SIZE, (page + 1) * PAGE_SIZE),
    [prefixes, page]
  );

  const cellStyle = (isSelected) => ({
    backgroundColor: isSelected ? '#cce5ff' : 'white',
    minHeight: '25px',
    display: 'flex',
    alignItems: 'center',
    justifyContent: 'flex-start',
    fontSize: '0.78rem',
    fontWeight: 400,
    padding: '0 10px',
    borderBottom: '1px dotted #ddd',
    cursor: 'pointer',
  });

  const headerStyle = {
    ...cellStyle(false),
    fontWeight: 'bold',
    backgroundColor: '#f0f0f0',
    borderBottom: '1px dotted #ccc',
    cursor: 'default',
  };

  const atmCashLabel = (rule) => {
    if (rule === '0' || rule === 0) return 'Destroy';
    if (rule === '1' || rule === 1) return 'Return';
    return 'N/A';
  };

  // Row selection (just for highlighting)
  const handleRowClick = (item) => {
    setSelectedPrefix(item.prefix);
  };

  // Open windows
  const openDetail = (item, e) => {
    e?.stopPropagation();
    setWinRow(item);
    setWinMode('detail');
    setWinOpen(true);
  };
  const openEdit = (item, e) => {
    e?.stopPropagation();
    setWinRow(item);
    setWinMode('edit');
    setWinOpen(true);
  };
  const openDelete = (item, e) => {
    e?.stopPropagation();
    setWinRow(item);
    setWinMode('delete');
    setWinOpen(true);
  };
  const openNew = () => {
    // start with an empty draft; prefill billingSp if present on the group
    setWinRow({ billingSp: selectedGroupRow?.billingSp || '', prefix: '', atmCashRule: '' });
    setWinMode('new');
    setWinOpen(true);
  };

  const isPrevDisabled = page <= 0;
  const isNextDisabled = pageCount === 0 || page >= pageCount - 1;

  return (
    <>
      <CRow className="mb-3">
        <CCol xs={12}>
          <div style={{ height: '320px', marginBottom: '4px', marginTop: '5px' }}>
            <div className="d-flex align-items-center" style={{ padding: '0.25rem 0.5rem', height: '100%' }}>
              <div style={{ width: '100%', height: '100%' }}>
                <div style={{ display: 'flex', flexDirection: 'column', height: '100%' }}>
                  <div
                    style={{
                      flex: 1,
                      display: 'grid',
                      gridTemplateColumns: '1fr 1fr 1fr 1fr',
                      rowGap: '0px',
                      columnGap: '4px',
                      minHeight: '250px',
                      alignContent: 'start',
                    }}
                  >
                    <div style={headerStyle}>Billing SP</div>
                    <div style={headerStyle}>Prefix</div>
                    <div style={headerStyle}>ATM/Cash</div>
                    <div style={headerStyle}>Action</div>

                    {pageData.map((item, index) => {
                      const isSelected = item.prefix === selectedPrefix;
                      return (
                        <React.Fragment key={`${item.billingSp}-${item.prefix}-${index}`}>
                          <div style={cellStyle(isSelected)} onClick={() => handleRowClick(item)}>
                            {item.billingSp}
                          </div>
                          <div style={cellStyle(isSelected)} onClick={() => handleRowClick(item)}>
                            {item.prefix}
                          </div>
                          <div style={cellStyle(isSelected)} onClick={() => handleRowClick(item)}>
                            {atmCashLabel(item.atmCashRule)}
                          </div>

                          {/* Action column */}
                          <div style={cellStyle(false)} onClick={(e) => e.stopPropagation()}>
                            <Tooltip title="Detail">
                              <IconButton size="small" aria-label="detail" onClick={(e) => openDetail(item, e)}>
                                <VisibilityIcon fontSize="small" />
                              </IconButton>
                            </Tooltip>
                            <Tooltip title="Edit">
                              <IconButton size="small" aria-label="edit" onClick={(e) => openEdit(item, e)}>
                                <EditIcon fontSize="small" />
                              </IconButton>
                            </Tooltip>
                            <Tooltip title="Delete">
                              <IconButton size="small" aria-label="delete" onClick={(e) => openDelete(item, e)}>
                                <DeleteIcon fontSize="small" />
                              </IconButton>
                            </Tooltip>
                          </div>
                        </React.Fragment>
                      );
                    })}
                  </div>

                  {/* Pager */}
                  <div
                    style={{
                      marginTop: '0px',
                      display: 'flex',
                      justifyContent: 'space-between',
                      alignItems: 'center',
                    }}
                  >
                    <Button
                      variant="text"
                      size="small"
                      sx={{ fontSize: '0.7rem', padding: '2px 8px', minWidth: 'unset', textTransform: 'none' }}
                      onClick={() => setPage((p) => Math.max(p - 1, 0))}
                      disabled={isPrevDisabled}
                    >
                      ◀ Previous
                    </Button>
                    <Typography fontSize="0.75rem">
                      Page {prefixes.length > 0 ? page + 1 : 0} of {prefixes.length > 0 ? pageCount : 0}
                    </Typography>
                    <Button
                      variant="text"
                      size="small"
                      sx={{ fontSize: '0.7rem', padding: '2px 8px', minWidth: 'unset', textTransform: 'none' }}
                      onClick={() => setPage((p) => Math.min(p + 1, Math.max(pageCount - 1, 0)))}
                      disabled={isNextDisabled}
                    >
                      Next ▶
                    </Button>
                  </div>

                  {/* New button centered under pager */}
                  <div
                    style={{
                      marginTop: '8px',
                      display: 'flex',
                      justifyContent: 'center',
                      alignItems: 'center',
                    }}
                  >
                    <Button
                      onClick={openNew}
                      variant="contained"
                      color="primary"
                      size="small"
                      sx={{ textTransform: 'none', fontSize: '0.8rem', px: 2.5, py: 0.75 }}
                    >
                      New
                    </Button>
                  </div>

                </div>
              </div>
            </div>
          </div>
        </CCol>
      </CRow>

      {/* Unified Window */}
      <AtmCashPrefixDetailWindow
        open={winOpen}
        mode={winMode}
        row={winRow}
        onClose={() => setWinOpen(false)}
        onConfirm={async (mode, draft) => {
          // UPDATE: reflect the change locally (and call your API for real)
          // Replace this mock with your PUT/POST update call if needed.
          setPrefixes((prev) => {
            const idx = prev.findIndex(
              (r) => r.billingSp === draft.billingSp && r.prefix === draft.prefix
            );
            const next = [...prev];
            if (idx >= 0) {
              next[idx] = { ...next[idx], ...draft };
            } else {
              next.push({ ...draft });
            }
            return next;
          });
          setSelectedPrefix(draft.prefix);
//          setWinOpen(false);
        }}
        onCreated={(created) => {
          setPrefixes((prev) => {
            const exists = prev.some(
              (r) => r.billingSp === created.billingSp && r.prefix === created.prefix
            );
            const next = exists
              ? prev.map((r) =>
                  r.billingSp === created.billingSp && r.prefix === created.prefix ? created : r
                )
              : [...prev, created];

            // Jump to the last page so the new row is visible (optional)
            const newPageCount = Math.ceil(next.length / PAGE_SIZE) || 0;
            setPage(Math.max(newPageCount - 1, 0));

            return next;
          });
          setSelectedPrefix(created.prefix);
//          setWinOpen(false);
        }}
        onDeleted={(deleted) => {
          setPrefixes((prev) => {
            const next = prev.filter(
              (r) => !(r.billingSp === deleted.billingSp && r.prefix === deleted.prefix)
            );

            // If we removed the last item on the page, pull back a page (keeps pager valid)
            const newPageCount = Math.ceil((next.length || 0) / PAGE_SIZE) || 0;
            setPage((p) => Math.min(p, Math.max(newPageCount - 1, 0)));

            // Clear selection if we deleted the selected row
            setSelectedPrefix((pfx) => (pfx === deleted.prefix ? '' : pfx));

            return next;
          });
 //         setWinOpen(false);
        }}
      />
    </>
  );
};

export default EditAtmCashPrefix;

