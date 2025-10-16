// ClientReportWindow.jsx
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
} from '@mui/material';

// NEW: use your autocomplete input
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

/**
 * Props:
 * - open: boolean
 * - mode: 'detail' | 'edit' | 'new' | 'delete'
 * - row: { reportName, receive, destination, fileText, email, password } | undefined
 * - onClose: () => void
 * - onSave: (updatedRow) => void
 * - onDelete?: (rowToDelete) => void
 */
const ClientReportWindow = ({
  open,
  mode = 'detail',
  row,
  onClose,
  onSave,
  onDelete,
}) => {
  // default empty row (for "new" mode or fallback)
  const EMPTY_ROW = {
    reportName: '',
    receive: '0',
    destination: '0',
    fileText: '0',
    email: '0',
    password: '',
  };

  // normalize incoming row to expected string codes
  const normalize = (r) => ({
    reportName: r?.reportName ?? '',
    receive: r?.receive != null ? String(r.receive) : '0',
    destination: r?.destination != null ? String(r.destination) : '0',
    fileText: r?.fileText != null ? String(r.fileText) : '0',
    email: r?.email != null ? String(r.email) : '0',
    password: r?.password ?? '',
  });

  // choose the effective source for the form by mode
  const effectiveSource = mode === 'new' ? EMPTY_ROW : (row ?? EMPTY_ROW);

  const [form, setForm] = useState(normalize(effectiveSource));
  useEffect(() => {
    setForm(normalize(mode === 'new' ? EMPTY_ROW : (row ?? EMPTY_ROW)));
  }, [row, mode]);

  // NEW: local state to drive ClientReportAutoCompleteInputBox when in "new" mode
  const [reportNameInput, setReportNameInput] = useState(form.reportName || '');
  const [isWildcardMode, setIsWildcardMode] = useState(false);

  // keep form.reportName in sync with the ClientReportAutoCompleteInputBox value
  useEffect(() => {
    if (mode === 'new') {
      setForm((prev) => ({ ...prev, reportName: reportNameInput || '' }));
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

  // Avoid rendering until form is ready
  const ready = useMemo(() => !!form, [form]);

  // label cell (detail/delete modes)
  const renderLabel = (options, value) => (
    <Typography variant="body2" sx={{ fontSize: '0.9rem' }}>
      {labelFor(options, value)}
    </Typography>
  );

  // select cell (edit/new modes)
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

  const handlePrimaryAction = () => {
    if (mode === 'delete') {
      onDelete?.(form);
      return;
    }
    onSave?.(form);
  };

  return (
    <Dialog open={open} onClose={onClose} maxWidth="sm" fullWidth>
      {/* Compact blue header */}
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
        <Typography
          component="span"
          sx={{ fontSize: '0.9rem', lineHeight: 2, fontWeight: 400, color: 'inherit', mb: 0 }}
        >
          {titleText}
        </Typography>
      </DialogTitle>

      <DialogContent dividers>
        {!ready ? (
          <Typography variant="body2">Loading…</Typography>
        ) : (
          <Box display="grid" gridTemplateColumns="1fr 1fr" gap={2} mt={0.5}>
            {/* Report Name */}
            <Box gridColumn="1 / span 2">
              <Typography sx={labelSx}>Report Name</Typography>

              {mode === 'new' ? (
                // Replaces the Select with your ClientReportAutoCompleteInputBox
                <ClientReportAutoCompleteInputBox
                  inputValue={reportNameInput}
                  setInputValue={setReportNameInput}
                  // The next props are optional safety no-ops so your component
                  // renders without requiring client searches in this context.
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
                  onChange={handleChange('reportName')}
                  size="small"
                  fullWidth
                />
              ) : (
                <Typography variant="body2" sx={{ fontSize: '0.9rem' }}>
                  {form.reportName || '(empty)'}
                </Typography>
              )}
            </Box>

            {/* Receive */}
            <Box>
              <Typography sx={labelSx}>Receive</Typography>
              {isEditable
                ? renderSelect(RECEIVE_OPTIONS, form.receive, 'receive')
                : renderLabel(RECEIVE_OPTIONS, form.receive)}
            </Box>

            {/* Destination */}
            <Box>
              <Typography sx={labelSx}>Destination</Typography>
              {isEditable
                ? renderSelect(DESTINATION_OPTIONS, form.destination, 'destination')
                : renderLabel(DESTINATION_OPTIONS, form.destination)}
            </Box>

            {/* File Type */}
            <Box>
              <Typography sx={labelSx}>File Type</Typography>
              {isEditable
                ? renderSelect(FILETYPE_OPTIONS, form.fileText, 'fileText')
                : renderLabel(FILETYPE_OPTIONS, form.fileText)}
            </Box>

            {/* Email */}
            <Box>
              <Typography sx={labelSx}>Email</Typography>
              {isEditable
                ? renderSelect(EMAIL_OPTIONS, form.email, 'email')
                : renderLabel(EMAIL_OPTIONS, form.email)}
            </Box>

            {/* Password */}
            <Box gridColumn="1 / span 2">
              <Typography sx={labelSx}>Password</Typography>
              {isEditable ? (
                <TextField
                  type="password"
                  value={form.password}
                  onChange={handleChange('password')}
                  size="small"
                  fullWidth
                />
              ) : (
                <Typography variant="body2" sx={{ fontSize: '0.9rem' }}>
                  {form.password ? '••••••••' : '(empty)'}
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
          </Box>
        )}
      </DialogContent>

      <DialogActions>
        <Button onClick={onClose} variant="outlined" size="small">
          {mode === 'delete' ? 'Cancel' : 'Close'}
        </Button>

        {mode === 'edit' && (
          <Button onClick={handlePrimaryAction} variant="contained" size="small">
            Save
          </Button>
        )}

        {mode === 'new' && (
          <Button
            onClick={handlePrimaryAction}
            variant="contained"
            size="small"
            disabled={!form.reportName} // still prevent creating without a selection/text
          >
            Create
          </Button>
        )}

        {mode === 'delete' && (
          <Button onClick={handlePrimaryAction} variant="contained" color="error" size="small">
            Delete
          </Button>
        )}
      </DialogActions>
    </Dialog>
  );
};

export default ClientReportWindow;




curl -X 'POST' \
  'http://localhost:8089/client-sysprin-writer/api/client/reportOptions/update' \
  -H 'accept: */*' \
  -H 'Content-Type: application/json' \
  -d '{
  "clientId": "0029",
  "reportId": 121,
  "receiveFlag": false,
  "outputTypeCd": 0,
  "fileTypeCd": 0,
  "emailFlag": 0,
  "emailBodyTx": "email body updated",
  "reportPasswordTx": "updates report for 0029"
}'



curl -X 'POST' \
  'http://localhost:8089/client-sysprin-writer/api/client/reportOptions/Initiate' \
  -H 'accept: */*' \
  -H 'Content-Type: application/json' \
  -d '{
    "clientId": "0029",
    "reportId": 115,
    "receiveFlag": false,
    "outputTypeCd": 0,
    "fileTypeCd": 0,
    "emailFlag": 0,
    "emailBodyTx": null,
    "reportPasswordTx": "new report for 0029"
}'







