// EditInvalidedAreaWindow.jsx
import React, { useMemo, useState } from 'react';
import {
  Dialog, DialogTitle, DialogContent, DialogActions,
  Button, Box, FormControl, InputLabel, Select, MenuItem, Typography
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

  // Return server payload if any, otherwise just the area we sent
  try {
    const ct = res.headers.get('Content-Type') || '';
    if (ct.includes('application/json')) return await res.json();
  } catch {}
  return { id: { sysPrin, area } };
}

/**
 * Props:
 * - open: boolean
 * - onClose: () => void
 * - sysPrin: string
 * - onCreated: (areaCode: string) => void   // caller updates UI list
 */
export default function EditInvalidedAreaWindow({ open, onClose, sysPrin, onCreated }) {
  const [area, setArea] = useState('');
  const [submitting, setSubmitting] = useState(false);

  const title = useMemo(
    () => `Add "Do Not Deliver" Area${sysPrin ? ` for ${sysPrin}` : ''}`,
    [sysPrin]
  );

  const handleCreate = async () => {
    if (!area || !sysPrin) return;
    setSubmitting(true);
    try {
      await createInvalidArea(sysPrin, area);
      onCreated?.(area);
      onClose?.();
      setArea('');
    } catch (e) {
      alert(e.message || 'Failed to create area.');
    } finally {
      setSubmitting(false);
    }
  };

  return (
    <Dialog open={open} onClose={submitting ? undefined : onClose} maxWidth="xs" fullWidth>
      <DialogTitle sx={{ fontSize: '1rem' }}>{title}</DialogTitle>
      <DialogContent dividers>
        <Box sx={{ display: 'grid', gap: 2 }}>
          <Typography variant="body2" sx={{ color: 'text.secondary' }}>
            Select a U.S. state to add to the "Do Not Deliver" list.
          </Typography>
          <FormControl fullWidth size="small">
            <InputLabel id="state-label">State</InputLabel>
            <Select
              labelId="state-label"
              label="State"
              value={area}
              onChange={(e) => setArea(e.target.value)}
              disabled={submitting}
            >
              {STATES_50.map((s) => (
                <MenuItem key={s} value={s}>{s}</MenuItem>
              ))}
            </Select>
          </FormControl>
        </Box>
      </DialogContent>
      <DialogActions>
        <Button onClick={onClose} disabled={submitting}>Cancel</Button>
        <Button
          onClick={handleCreate}
          disabled={!area || !sysPrin || submitting}
          variant="contained"
        >
          Create
        </Button>
      </DialogActions>
    </Dialog>
  );
}
