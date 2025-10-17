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
          reportId: payload.reportId,
          receive: form.receive,
          destination: form.destination,
          fileText: form.fileText,
          email: form.email,
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

            {/* Email Body (replaces old Password area; spans both columns) */}
            <Box gridColumn="1 / span 2">
              <Typography sx={labelSx}>Email Body</Typography>
              {isEditable ? (
                <TextField
                  value={form.emailBodyTx}
                  onChange={(e) => setForm((p) => ({ ...p, emailBodyTx: e.target.value }))}
                  size="small"
                  fullWidth
                  multiline
                  rows={3}
                />
              ) : (
                <Typography variant="body2" sx={{ fontSize: '0.9rem', whiteSpace: 'pre-wrap' }}>
                  {form.emailBodyTx || '(empty)'}
                </Typography>
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
