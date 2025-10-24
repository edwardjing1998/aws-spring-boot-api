// EditInvalidedAreaWindow.jsx
import React, { useEffect, useMemo, useState } from 'react';
import {
  Dialog, DialogTitle, DialogContent, DialogActions,
  Button, Box, FormControl, InputLabel, Select, MenuItem, Typography, Alert
} from '@mui/material';

const STATES_50 = [
  'AL','AK','AZ','AR','CA','CO','CT','DE','FL','GA',
  'HI','ID','IL','IN','IA','KS','KY','LA','ME','MD',
  'MA','MI','MN','MS','MO','MT','NE','NV','NH','NJ',
  'NM','NY','NC','ND','OH','OK','OR','PA','RI','SC',
  'SD','TN','TX','UT','VT','VA','WA','WV','WI','WY'
];

/** POST /api/sysprins/{sysPrin}/invalid-areas  { area, sysPrin } */
async function createInvalidArea(sysPrin, area) {
  const url = `http://localhost:8089/client-sysprin-writer/api/sysprins/${encodeURIComponent(sysPrin)}/invalid-areas`;
  const res = await fetch(url, {
    method: 'POST',
    headers: {
      accept: '*/*',
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({ area, sysPrin }),
  });

  if (!res.ok) {
    let msg = `Request failed (${res.status})`;
    try {
      const ct = res.headers.get('Content-Type') || '';
      if (ct.includes('application/json')) {
        const j = await res.json();
        msg = j?.message || JSON.stringify(j);
      } else {
        msg = await res.text();
      }
    } catch {}
    throw new Error(msg || 'Create failed');
  }

  try {
    const ct = res.headers.get('Content-Type') || '';
    if (ct.includes('application/json')) return await res.json();
  } catch {}
  return { id: { sysPrin, area } };
}

/** DELETE /api/sysprins/{sysPrin}/invalid-areas/delete  { area, sysPrin } */
async function deleteInvalidArea(sysPrin, area) {
  const url = `http://localhost:8089/client-sysprin-writer/api/sysprins/${encodeURIComponent(sysPrin)}/invalid-areas/delete`;
  const res = await fetch(url, {
    method: 'DELETE',
    headers: {
      accept: '*/*',
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({ area, sysPrin }),
  });

  if (!res.ok) {
    let msg = `Request failed (${res.status})`;
    try {
      const ct = res.headers.get('Content-Type') || '';
      if (ct.includes('application/json')) {
        const j = await res.json();
        msg = j?.message || JSON.stringify(j);
      } else {
        msg = await res.text();
      }
    } catch {}
    throw new Error(msg || 'Delete failed');
  }

  // server may return text; treat ok as success
  try {
    const ct = res.headers.get('Content-Type') || '';
    if (ct.includes('application/json')) return await res.json();
  } catch {}
  return { ok: true };
}

/**
 * Props:
 * - open: boolean
 * - onClose: () => void
 * - sysPrin: string
 * - onCreated?: (areaCode: string) => void   // for create success
 * - onDeleted?: (areaCode: string) => void   // for delete success
 * - mode?: 'create' | 'delete'               // default 'create'
 * - initialArea?: string                     // pre-select / lock area in delete mode
 */
export default function EditInvalidedAreaWindow({
  open,
  onClose,
  sysPrin,
  onCreated,
  onDeleted,
  mode = 'create',
  initialArea = '',
}) {
  const [area, setArea] = useState(initialArea || '');
  const [submitting, setSubmitting] = useState(false);
  const [successMsg, setSuccessMsg] = useState('');
  const [errorMsg, setErrorMsg] = useState('');

  // keep local area in sync when dialog opens in delete mode with a pre-set area
  useEffect(() => {
    if (open) {
      setArea(initialArea || '');
      setSuccessMsg('');
      setErrorMsg('');
    }
  }, [open, initialArea]);

  const isDelete = mode === 'delete';

  const title = useMemo(() => {
    if (isDelete) {
      return `Delete "Do Not Deliver" Area${sysPrin ? ` for ${sysPrin}` : ''}`;
    }
    return `Add "Do Not Deliver" Area${sysPrin ? ` for ${sysPrin}` : ''}`;
  }, [isDelete, sysPrin]);

  const handlePrimary = async () => {
    if (!area || !sysPrin) return;
    setSubmitting(true);
    setSuccessMsg('');
    setErrorMsg('');
    try {
      if (isDelete) {
        await deleteInvalidArea(sysPrin, area);
        onDeleted?.(area);
        setSuccessMsg(`The record (${area}) was deleted successfully.`);
      } else {
        await createInvalidArea(sysPrin, area);
        onCreated?.(area);
        setSuccessMsg(`The record (${area}) was created successfully.`);
      }
    } catch (e) {
      setErrorMsg(e.message || (isDelete ? 'Failed to delete area.' : 'Failed to create area.'));
    } finally {
      setSubmitting(false);
    }
  };

  return (
    <Dialog open={open} onClose={submitting ? undefined : onClose} maxWidth="xs" fullWidth>
      <DialogTitle sx={{ fontSize: '1rem' }}>{title}</DialogTitle>
      <DialogContent dividers>
        <Box sx={{ display: 'grid', gap: 2 }}>
          {successMsg && <Alert severity="success">{successMsg}</Alert>}
          {errorMsg && <Alert severity="error">{errorMsg}</Alert>}

          {isDelete ? (
            <>
              <Typography variant="body2" sx={{ color: 'text.secondary' }}>
                You are about to <strong>delete</strong> the area below from the "Do Not Deliver" list.
              </Typography>
              <FormControl fullWidth size="small" disabled>
                <InputLabel id="state-label">State</InputLabel>
                <Select labelId="state-label" label="State" value={area}>
                  <MenuItem value={area}>{area}</MenuItem>
                </Select>
              </FormControl>
              <Alert severity="warning">
                This action cannot be undone.
              </Alert>
            </>
          ) : (
            <>
              <Typography variant="body2" sx={{ color: 'text.secondary' }}>
                Select a U.S. state to add to the "Do Not Deliver" list.
              </Typography>
              <FormControl fullWidth size="small">
                <InputLabel id="state-label">State</InputLabel>
                <Select
                  labelId="state-label"
                  label="State"
                  value={area}
                  onChange={(e) => {
                    setArea(e.target.value);
                    setSuccessMsg('');
                    setErrorMsg('');
                  }}
                  disabled={submitting}
                >
                  {STATES_50.map((s) => (
                    <MenuItem key={s} value={s}>{s}</MenuItem>
                  ))}
                </Select>
              </FormControl>
            </>
          )}
        </Box>
      </DialogContent>
      <DialogActions>
        <Button onClick={onClose} disabled={submitting}>Close</Button>
        <Button
          onClick={handlePrimary}
          disabled={!area || !sysPrin || submitting}
          variant={isDelete ? 'outlined' : 'contained'}
          color={isDelete ? 'error' : 'primary'}
        >
          {isDelete ? 'Delete' : 'Create'}
        </Button>
      </DialogActions>
    </Dialog>
  );
}
