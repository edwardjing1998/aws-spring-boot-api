import React, { useMemo, useState, useEffect } from 'react';
import {
  Dialog,
  DialogTitle,
  DialogContent,
  DialogActions,
  Button,
  Box,
  TextField,
  FormControl,
  Select,
  MenuItem,
  Typography,
  Alert,
  AlertColor,
  SelectChangeEvent,
} from '@mui/material';

// Assuming this path based on typical project structure; adjust if necessary
import ClientReportAutoCompleteInputBox from './ClientReportAutoCompleteInputBox';
import { ClientReportRow } from './EditClientReport.types';

/* =======================
   Option Maps
======================= */
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

interface OptionItem {
  value: string;
  label: string;
}

const labelFor = (options: OptionItem[], v: string | undefined | null) => 
  (options.find(o => o.value === String(v))?.label ?? 'None');

/* =======================
   Helpers
======================= */
const extractReportId = (val: string | undefined | null, fallback: number | null): number | null => {
  if (val == null) return fallback ?? null;
  const s = String(val).trim();
  const m = s.match(/^(\d+)/);
  return m ? Number(m[1]) : (fallback ?? null);
};

// NEW: extract fileExt from autocomplete option string
// format: `${reportId} :::: ${name}  :::: ${fileExt}`
const extractFileExt = (val: string | null | undefined): string => {
  if (!val) return '';
  const s = String(val);
  const parts = s.split(' :::: ');
  if (parts.length >= 3) return parts[2].trim();
  return '';
};

const extractName = (val: string | null | undefined): string => {
  if (!val) return '';
  const s = String(val);
  const parts = s.split(' :::: ');
  if (parts.length >= 3) return parts[1].trim();
  return '';
};

const toBool = (code: string | undefined): boolean => String(code) === '1';
const toInt = (code: string | undefined, def = 0): number => {
  const n = Number(code);
  return Number.isFinite(n) ? n : def;
};

/* =======================
   Component
======================= */

export interface ClientReportWindowProps {
  open: boolean;
  mode?: 'detail' | 'edit' | 'new' | 'delete';
  row: ClientReportRow | null;
  clientId: string;
  onClose: () => void;
  onSave?: (data: ClientReportRow) => void;
  onDelete?: () => void;
}

interface StatusMessage {
  severity: AlertColor;
  text: string;
}

const ClientReportWindow: React.FC<ClientReportWindowProps> = ({
  open,
  mode = 'detail',
  row,
  clientId,
  onClose,
  onSave,
  onDelete,
}) => {

  const EMPTY_ROW: ClientReportRow = {
    reportName: '',
    reportId: null,
    receive: '0',
    destination: '0',
    fileText: '0',
    email: '0',
    password: '',
    emailBodyTx: '',
    fileExt: '',
  };

  const normalize = (r: ClientReportRow | null | undefined): ClientReportRow => ({
    reportName: r?.reportName ?? '',
    reportId: r?.reportId ?? extractReportId(r?.reportName ?? '', null),
    receive: String(r?.receive ?? '0'),
    destination: String(r?.destination ?? '0'),
    fileText: String(r?.fileText ?? '0'),
    email: String(r?.email ?? '0'),
    password: r?.password ?? '',
    emailBodyTx: r?.emailBodyTx ?? '',
    fileExt: r?.fileExt ?? '',   // include extension
  });

  const effectiveSource = mode === 'new' ? EMPTY_ROW : (row ?? EMPTY_ROW);

  const [form, setForm] = useState<ClientReportRow>(normalize(effectiveSource));
  
  useEffect(() => {
    setForm(normalize(mode === 'new' ? EMPTY_ROW : (row ?? EMPTY_ROW)));
  }, [row, mode]);

  /* =======================
     AutoComplete "new" mode
  ======================= */
  const [reportNameInput, setReportNameInput] = useState<string>(form.reportName);
  const [isWildcardMode, setIsWildcardMode] = useState(false);

  // When selecting from autocomplete, extract reportId + fileExt
  useEffect(() => {
    if (mode === 'new') {
      const rid = extractReportId(reportNameInput, null);
      const ext = extractFileExt(reportNameInput);
      const name = extractName(reportNameInput);

      setForm((prev) => ({
        ...prev,
        reportName: name,
        reportId: rid,
        fileExt: ext,                    // <-- update form with extracted extension
      }));
    }
  }, [reportNameInput, mode]);

  const isReadOnly = mode === 'detail' || mode === 'delete';
  const isEditable = mode === 'edit' || mode === 'new';

  const cellSelectSx = {
    fontSize: '0.85rem',
    backgroundColor: 'white',
    '& .MuiSelect-select': { padding: '6px 10px', fontSize: '0.85rem' },
  };
  const labelSx = { fontSize: '0.8rem', color: '#666', mb: 0.5 };

  const handleChange = (key: keyof ClientReportRow) => (e: SelectChangeEvent | React.ChangeEvent<HTMLInputElement | HTMLTextAreaElement>) => {
    setForm((prev) => ({ ...prev, [key]: String(e.target.value) }));
  }

  const renderLabel = (options: OptionItem[], value: string) => (
    <Typography variant="body2" sx={{ fontSize: '0.9rem' }}>
      {labelFor(options, value)}
    </Typography>
  );

  const renderSelect = (options: OptionItem[], value: string, key: keyof ClientReportRow) => (
    <FormControl fullWidth size="small">
      <Select 
        value={value} 
        onChange={handleChange(key) as any} // Cast needed for Select vs TextField event diffs if generic handler used
        sx={cellSelectSx} 
        disabled={!isEditable}
      >
        {options.map(opt => (
          <MenuItem key={opt.value} value={opt.value} sx={{ fontSize: '0.85rem' }}>
            {opt.label}
          </MenuItem>
        ))}
      </Select>
    </FormControl>
  );

  /* =======================
     API + Save Handlers
  ======================= */
  const [submitting, setSubmitting] = useState(false);
  const [statusMessage, setStatusMessage] = useState<StatusMessage | null>(null);

  useEffect(() => { setStatusMessage(null); }, [mode, open]);

  const buildPayload = () => ({
    clientId: clientId ?? '',
    reportId: form.reportId,
    receiveFlag: toBool(form.receive),
    outputTypeCd: toInt(form.destination),
    fileTypeCd: toInt(form.fileText),
    emailFlag: toInt(form.email),
    emailBodyTx: form.emailBodyTx,
    reportPasswordTx: form.password,
    fileExt: form.fileExt,   // <-- include fileExt in API payload
  });

  const postJson = async (url: string, body: any) => {
    const res = await fetch(url, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json', accept: '*/*' },
      body: JSON.stringify(body),
    });
    if (!res.ok) throw new Error(await res.text());
    try { return await res.json(); } catch { return null; }
  };

  const deleteCall = async (url: string) => {
    const res = await fetch(url, { method: 'DELETE', headers: { accept: '*/*' } });
    if (!res.ok) throw new Error(await res.text());
    return null;
  };

  /* =======================
     Submit / Save
  ======================= */
  const handlePrimaryAction = async () => {

    const rid = form.reportId;

    if (mode === 'new' && (!rid || !form.reportName)) {
      setStatusMessage({ severity: 'error', text: 'Please select a report first.' });
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
        onSave?.({ ...form });   // <-- send form INCLUDING fileExt
        setStatusMessage({ severity: 'success', text: 'Report created successfully.' });

      } else if (mode === 'edit') {
        await postJson(
          'http://localhost:8089/client-sysprin-writer/api/client/reportOptions/update',
          payload
        );
        onSave?.({ ...form });
        setStatusMessage({ severity: 'success', text: 'Report updated successfully.' });
      } else if (mode === 'delete') {
        if (!clientId || !rid) {
             throw new Error("Missing Client ID or Report ID for deletion.");
        }
        await deleteCall(
           `http://localhost:8089/client-sysprin-writer/api/client/reportOptions/delete/${encodeURIComponent(clientId)}/${encodeURIComponent(String(rid))}`
        );
        onDelete?.(); // notify parent
        setStatusMessage({ severity: 'success', text: 'Report deleted successfully.' });
      }

    } catch (err: any) {
      setStatusMessage({ severity: 'error', text: `Submit failed: ${err.message}` });
    } finally {
      setSubmitting(false);
    }
  };

  const primaryDisabled =
    submitting ||
    (mode === 'new' && (!form.reportName || !form.reportId));

  const titleText =
    mode === 'edit' ? 'Edit Client Report'
    : mode === 'new' ? 'New Client Report'
    : mode === 'delete' ? 'Delete Client Report'
    : 'Report Details';

  /* =======================
     Render
  ======================= */
  return (
    <Dialog open={open} onClose={onClose} maxWidth="sm" fullWidth>
      <DialogTitle sx={{ bgcolor: '#1976d2', color: 'white', py: 1 }}>
        {titleText}
      </DialogTitle>

      <DialogContent dividers>

        {statusMessage && (
          <Alert severity={statusMessage.severity} sx={{ mb: 1 }}>
            {statusMessage.text}
          </Alert>
        )}

        <Box display="grid" gridTemplateColumns="1fr 1fr" gap={2} mt={0.5}>

          {/* Report Name */}
          <Box>
            <Typography sx={labelSx}>Report Name</Typography>
            {mode === 'new' ? (
              <ClientReportAutoCompleteInputBox
                inputValue={reportNameInput}
                setInputValue={setReportNameInput}
                isWildcardMode={isWildcardMode}
                setIsWildcardMode={setIsWildcardMode}
                onClientsFetched={() => {}}
              />
            ) : isEditable ? (
              <TextField
                value={form.reportName}
                onChange={(e) => {
                  const v = e.target.value;
                  setForm((prev) => ({
                    ...prev,
                    reportName: v,
                    reportId: extractReportId(v, prev.reportId),
                  }));
                }}
                size="small"
                fullWidth
              />
            ) : (
              <Typography sx={{ fontSize: '0.9rem' }}>{form.reportName}</Typography>
            )}
          </Box>

          {/* Password */}
          <Box>
            <Typography sx={labelSx}>Password</Typography>
            {isEditable ? (
              <TextField
                type="password"
                size="small"
                fullWidth
                value={form.password}
                onChange={(e) => setForm((p) => ({ ...p, password: e.target.value }))}
              />
            ) : (
              <Typography sx={{ fontSize: '0.9rem' }}>
                {form.password ? '••••••••' : '(empty)'}
              </Typography>
            )}
          </Box>

          {/* Receive */}
          <Box>
            <Typography sx={labelSx}>Receive</Typography>
            {isEditable
              ? renderSelect(RECEIVE_OPTIONS, form.receive, 'receive')
              : renderLabel(RECEIVE_OPTIONS, form.receive)
            }
          </Box>

          {/* Destination */}
          <Box>
            <Typography sx={labelSx}>Destination</Typography>
            {isEditable
              ? renderSelect(DESTINATION_OPTIONS, form.destination, 'destination')
              : renderLabel(DESTINATION_OPTIONS, form.destination)
            }
          </Box>

          {/* File Type */}
          <Box>
            <Typography sx={labelSx}>File Type</Typography>
            {isEditable
              ? renderSelect(FILETYPE_OPTIONS, form.fileText, 'fileText')
              : renderLabel(FILETYPE_OPTIONS, form.fileText)
            }
          </Box>

          {/* Email */}
          <Box>
            <Typography sx={labelSx}>Email</Typography>
            {isEditable
              ? renderSelect(EMAIL_OPTIONS, form.email, 'email')
              : renderLabel(EMAIL_OPTIONS, form.email)
            }
          </Box>

          {/* NEW: File Extension */}
          <Box>
            <Typography sx={labelSx}>File Extension</Typography>
            {isEditable ? (
              <TextField
                value={form.fileExt}
                onChange={(e) => setForm((p) => ({ ...p, fileExt: e.target.value }))}
                size="small"
                fullWidth
              />
            ) : (
              <Typography sx={{ fontSize: '0.9rem' }}>
                {form.fileExt || '(empty)'}
              </Typography>
            )}
          </Box>

          {/* Email Body */}
          <Box gridColumn="1 / span 2">
            <Typography sx={labelSx}>Email Body</Typography>
            <TextField
              size="small"
              fullWidth
              multiline
              rows={3}
              value={form.emailBodyTx}
              onChange={(e) => setForm((p) => ({ ...p, emailBodyTx: e.target.value }))}
              inputProps={{ maxLength: 500 }}
              helperText={`${(form.emailBodyTx?.length ?? 0)}/500`}
            />
          </Box>

          {mode === 'delete' && (
            <Box gridColumn="1 / span 2" sx={{ mt: 1 }}>
              <Typography color="error">
                This action cannot be undone. Delete this report?
              </Typography>
            </Box>
          )}
        </Box>
      </DialogContent>

      {/* Buttons */}
      <DialogActions>
        <Button onClick={onClose} size="small" variant="outlined">Close</Button>

        {mode === 'edit' && (
          <Button onClick={handlePrimaryAction} size="small" variant="contained">Save</Button>
        )}
        {mode === 'new' && (
          <Button onClick={handlePrimaryAction} size="small" variant="contained" disabled={primaryDisabled}>
            Create
          </Button>
        )}
        {mode === 'delete' && (
          <Button onClick={handlePrimaryAction} size="small" color="error" variant="contained">
            Delete
          </Button>
        )}
      </DialogActions>
    </Dialog>
  );
};

export default ClientReportWindow;




import environment from '../../Environments/Environment';

const CLIENT_SYSPRIN_WRITER_API_BASE = environment.clientWriterBaseUrl;

export interface ClientReportPayload {
  clientId: string;
  reportId: number | null;
  receiveFlag: boolean;
  outputTypeCd: number;
  fileTypeCd: number;
  emailFlag: number;
  emailBodyTx: string;
  reportPasswordTx: string;
  fileExt: string;
}

async function requestJson(url: string, options: RequestInit) {
  const res = await fetch(url, {
    headers: {
      accept: '*/*',
      ...(options.body ? { 'Content-Type': 'application/json' } : {}),
      ...(options.headers || {}),
    },
    ...options,
  });

  if (!res.ok) {
    const text = await res.text().catch(() => '');
    throw new Error(text || `${options.method || 'GET'} ${url} failed: ${res.status}`);
  }

  // backend may return json or empty body
  const text = await res.text().catch(() => '');
  if (!text) return null;

  try {
    return JSON.parse(text);
  } catch {
    return text; // sometimes backend returns plain text
  }
}

export async function createClientReport(payload: ClientReportPayload) {
  const url = `${CLIENT_SYSPRIN_WRITER_API_BASE}/client/reportOptions/Initiate`;
  return requestJson(url, { method: 'POST', body: JSON.stringify(payload) });
}

export async function updateClientReport(payload: ClientReportPayload) {
  const url = `${CLIENT_SYSPRIN_WRITER_API_BASE}/client/reportOptions/update`;
  return requestJson(url, { method: 'POST', body: JSON.stringify(payload) });
}

export async function deleteClientReport(clientId: string, reportId: number) {
  const url = `${CLIENT_SYSPRIN_WRITER_API_BASE}/client/reportOptions/delete/${encodeURIComponent(
    clientId,
  )}/${encodeURIComponent(String(reportId))}`;

  return requestJson(url, { method: 'DELETE' });
}
