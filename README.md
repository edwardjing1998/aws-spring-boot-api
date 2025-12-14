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
  AlertColor,
  SelectChangeEvent,
} from '@mui/material';

// Import shared type from parent directory
import { AtmCashPrefixRow } from '../EditAtmCashPrefix.types';

const atmCashLabel = (rule: string | number | undefined) => {
  if (rule === '0' || rule === 0) return 'Destroy';
  if (rule === '1' || rule === 1) return 'Return';
  return 'N/A';
};

export interface AtmCashPrefixDetailWindowProps {
  open: boolean;
  mode?: 'detail' | 'edit' | 'delete' | 'new';
  row: AtmCashPrefixRow | null;
  onClose: () => void;
  onConfirm?: (mode: string, draftRow: AtmCashPrefixRow) => Promise<void> | void;
  onCreated?: (createdRow: AtmCashPrefixRow) => void;
  onDeleted?: (deletedRow: AtmCashPrefixRow) => void;
}

interface StatusState {
  severity: AlertColor;
  text: string;
}

const AtmCashPrefixDetailWindow: React.FC<AtmCashPrefixDetailWindowProps> = ({
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
  const [draft, setDraft] = useState<AtmCashPrefixRow>({ billingSp: '', prefix: '', atmCashRule: '' });
  const [submitting, setSubmitting] = useState(false);
  const [status, setStatus] = useState<StatusState | null>(null);

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
    } catch (err: any) {
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
    } catch (err: any) {
      console.error('Delete failed:', err);
      setStatus({ severity: 'error', text: `Delete failed: ${err.message}` });
    } finally {
      setSubmitting(false);
    }
  };

  // EDIT (PUT)
  const handleEditConfirm = async () => {
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
      await Promise.resolve(onConfirm?.('edit', { ...draft }));
    } catch (err: any) {
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

        {!row && mode !== 'new' ? (
          <Typography sx={{ fontSize: '0.9rem' }}>(No data)</Typography>
        ) : isDetail ? (
          <Box display="grid" gridTemplateColumns="1fr 1fr" gap={1}>
            <Typography sx={{ fontSize: '0.8rem', color: '#666' }}>Billing SP</Typography>
            <Typography sx={{ fontSize: '0.9rem' }}>{row?.billingSp ?? '(empty)'}</Typography>

            <Typography sx={{ fontSize: '0.8rem', color: '#666' }}>Prefix</Typography>
            <Typography sx={{ fontSize: '0.9rem' }}>{row?.prefix ?? '(empty)'}</Typography>

            <Typography sx={{ fontSize: '0.8rem', color: '#666' }}>ATM/Cash</Typography>
            <Typography sx={{ fontSize: '0.9rem' }}>{atmCashLabel(row?.atmCashRule)}</Typography>
          </Box>
        ) : (isEdit || isNew) ? (
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
                  onChange={(e: SelectChangeEvent) => setDraft((d) => ({ ...d, atmCashRule: e.target.value }))}
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
          <Box>
            <Typography sx={{ fontSize: '0.9rem', mb: 1 }}>
              Are you sure you want to delete this prefix?
            </Typography>
            <Box display="grid" gridTemplateColumns="1fr 1fr" gap={1}>
              <Typography sx={{ fontSize: '0.8rem', color: '#666' }}>Billing SP</Typography>
              <Typography sx={{ fontSize: '0.9rem' }}>{row?.billingSp ?? '(empty)'}</Typography>

              <Typography sx={{ fontSize: '0.8rem', color: '#666' }}>Prefix</Typography>
              <Typography sx={{ fontSize: '0.9rem' }}>{row?.prefix ?? '(empty)'}</Typography>

              <Typography sx={{ fontSize: '0.8rem', color: '#666' }}>ATM/Cash</Typography>
              <Typography sx={{ fontSize: '0.9rem' }}>
                {atmCashLabel(row?.atmCashRule)}
              </Typography>
            </Box>
          </Box>
        )}
      </DialogContent>

      <DialogActions>
        <Button onClick={onClose} variant="outlined" size="small" disabled={submitting} sx={{ textTransform: 'none' }}>
          {isDelete ? 'Cancel' : 'Close'}
        </Button>

        {isNew && (
          <Button onClick={handleCreate} variant="contained" size="small" disabled={submitting} sx={{ textTransform: 'none' }}>
            {submitting ? 'Creating…' : 'Create'}
          </Button>
        )}

        {isEdit && (
          <Button onClick={handleEditConfirm} variant="contained" size="small" disabled={submitting} sx={{ textTransform: 'none' }}>
            Save
          </Button>
        )}

        {isDelete && (
          <Button onClick={handleDelete} color="error" variant="contained" size="small" disabled={submitting} sx={{ textTransform: 'none' }}>
            {submitting ? 'Deleting…' : 'Delete'}
          </Button>
        )}
      </DialogActions>
    </Dialog>
  );
};

export default AtmCashPrefixDetailWindow;
