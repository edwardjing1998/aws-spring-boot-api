// SysPrinEditButtonPanel.jsx
import React from 'react';
import { CCard, CCardBody } from '@coreui/react';
import { Button } from '@mui/material';

const SysPrinEditButtonPanel = ({ setSysPrinInformationWindow }) => {
  const openWin = (mode) => setSysPrinInformationWindow({ open: true, mode });

  return (
    <CCard style={{ height: '35px', marginBottom: '4px', marginTop: '25px' }}>
      <CCardBody className="d-flex align-items-center" style={{ padding: '0.25rem 0.5rem', height: '100%' }}>
        <div>
          <Button variant="outlined" onClick={() => openWin('changeAll')}
            size="small" sx={{ fontSize: '0.78rem', mr: '6px', textTransform: 'none' }}>
            Change All
          </Button>

          <Button variant="outlined" onClick={() => openWin('edit')}
            size="small" sx={{ fontSize: '0.78rem', mr: '6px', textTransform: 'none' }}>
            Edit SysPrin
          </Button>

          <Button variant="outlined" onClick={() => openWin('new')}
            size="small" sx={{ fontSize: '0.78rem', mr: '6px', textTransform: 'none' }}>
            New SysPrin
          </Button>

          <Button variant="outlined" onClick={() => openWin('duplicate')}
            size="small" sx={{ fontSize: '0.78rem', mr: '6px', textTransform: 'none' }}>
            Duplicate
          </Button>

          <Button variant="outlined" onClick={() => openWin('move')}
            size="small" sx={{ fontSize: '0.78rem', mr: '6px', textTransform: 'none' }}>
            Move
          </Button>
        </div>
      </CCardBody>
    </CCard>
  );
};

export default SysPrinEditButtonPanel;





// src/components/sysprin/SysPrinInformationWindow.jsx
import React, { useEffect, useMemo, useState } from 'react';
import {
  Dialog,
  DialogTitle,
  DialogContent,
  DialogActions,
  Divider,
  Box,
  Typography,
  TextField,
  Select,
  MenuItem,
  FormControl,
  InputLabel,
  Button,
  Alert,
} from '@mui/material';

/**
 * Props:
 * - open: boolean
 * - mode: 'changeAll' | 'edit' | 'new' | 'duplicate' | 'move'
 * - initialRow?: {
 *     client?: string,
 *     sysPrin?: string,
 *     // any other editable fields you care about, e.g. notes, active, etc.
 *     notes?: string,
 *     active?: boolean | null
 *   }
 * - onClose: () => void
 *
 * Optional per-action callbacks (returning a Promise is supported):
 * - onChangeAll?: (payload: { client: string, templateSysPrin: string }) => Promise<void> | void
 * - onEdit?:      (payload: { client: string, sysPrin: string, notes?: string, active?: boolean|null }) => Promise<void> | void
 * - onCreate?:    (payload: { client: string, sysPrin: string, notes?: string, active?: boolean|null }) => Promise<void> | void
 * - onDuplicate?: (payload: { client: string, sourceSysPrin: string, targetSysPrin: string, copyAreas: boolean, overwrite: boolean }) => Promise<void> | void
 * - onMove?:      (payload: { oldClient: string, sysPrin: string, newClient: string }) => Promise<void> | void
 */
const SysPrinInformationWindow = ({
  open,
  mode = 'edit',
  initialRow = {},
  onClose,
  onChangeAll,
  onEdit,
  onCreate,
  onDuplicate,
  onMove,
}) => {
  const titleMap = {
    changeAll: 'Change All Sys/Prins',
    edit: 'Edit Sys/Prin',
    new: 'New Sys/Prin',
    duplicate: 'Duplicate Sys/Prin',
    move: 'Move Sys/Prin',
  };

  const title = useMemo(() => titleMap[mode] ?? 'Sys/Prin', [mode]);

  // Local draft state for different modes
  const [draft, setDraft] = useState({
    // common-ish fields
    client: '',
    sysPrin: '',
    notes: '',
    active: '', // '', true, or false -> empty means not set
    // changeAll
    templateSysPrin: '',
    // duplicate
    sourceSysPrin: '',
    targetSysPrin: '',
    copyAreas: 'true', // 'true' | 'false' for easy Select binding
    overwrite: 'false',
    // move
    oldClient: '',
    newClient: '',
  });

  const [submitting, setSubmitting] = useState(false);
  const [status, setStatus] = useState(null); // {severity: 'info'|'success'|'warning'|'error', text: string}

  useEffect(() => {
    if (!open) return;
    setStatus(null);

    // Seed defaults based on mode + initialRow
    const base = {
      client: initialRow.client ?? '',
      sysPrin: initialRow.sysPrin ?? '',
      notes: initialRow.notes ?? '',
      active:
        typeof initialRow.active === 'boolean'
          ? String(initialRow.active) // "true"/"false"
          : '', // not set
      templateSysPrin: initialRow.sysPrin ?? '',
      sourceSysPrin: initialRow.sysPrin ?? '',
      targetSysPrin: '',
      copyAreas: 'true',
      overwrite: 'false',
      oldClient: initialRow.client ?? '',
      newClient: '',
    };

    setDraft(base);
  }, [open, mode, initialRow]);

  const isChangeAll = mode === 'changeAll';
  const isEdit = mode === 'edit';
  const isNew = mode === 'new';
  const isDuplicate = mode === 'duplicate';
  const isMove = mode === 'move';

  // Handlers per mode (no auto-close; only set status)
  const handleSubmit = async () => {
    try {
      setSubmitting(true);
      setStatus({ severity: 'info', text: 'Working…' });

      if (isChangeAll) {
        if (!draft.client || !draft.templateSysPrin) {
          setStatus({ severity: 'warning', text: 'Client and Template Sys/Prin are required.' });
          return;
        }
        await Promise.resolve(
          onChangeAll?.({ client: draft.client, templateSysPrin: draft.templateSysPrin })
        );
        setStatus({ severity: 'success', text: 'Change All completed.' });
      }

      if (isEdit) {
        if (!draft.client || !draft.sysPrin) {
          setStatus({ severity: 'warning', text: 'Client and Sys/Prin are required.' });
          return;
        }
        await Promise.resolve(
          onEdit?.({
            client: draft.client,
            sysPrin: draft.sysPrin,
            notes: draft.notes || undefined,
            active: draft.active === '' ? null : draft.active === 'true',
          })
        );
        setStatus({ severity: 'success', text: 'Saved successfully.' });
      }

      if (isNew) {
        if (!draft.client || !draft.sysPrin) {
          setStatus({ severity: 'warning', text: 'Client and Sys/Prin are required.' });
          return;
        }
        await Promise.resolve(
          onCreate?.({
            client: draft.client,
            sysPrin: draft.sysPrin,
            notes: draft.notes || undefined,
            active: draft.active === '' ? null : draft.active === 'true',
          })
        );
        setStatus({ severity: 'success', text: 'Created successfully.' });
      }

      if (isDuplicate) {
        if (!draft.client || !draft.sourceSysPrin || !draft.targetSysPrin) {
          setStatus({ severity: 'warning', text: 'Client, Source, and Target Sys/Prin are required.' });
          return;
        }
        await Promise.resolve(
          onDuplicate?.({
            client: draft.client,
            sourceSysPrin: draft.sourceSysPrin,
            targetSysPrin: draft.targetSysPrin,
            copyAreas: draft.copyAreas === 'true',
            overwrite: draft.overwrite === 'true',
          })
        );
        setStatus({ severity: 'success', text: 'Duplicated successfully.' });
      }

      if (isMove) {
        if (!draft.oldClient || !draft.newClient || !draft.sysPrin) {
          setStatus({ severity: 'warning', text: 'Old Client, New Client, and Sys/Prin are required.' });
          return;
        }
        await Promise.resolve(
          onMove?.({
            oldClient: draft.oldClient,
            sysPrin: draft.sysPrin,
            newClient: draft.newClient,
          })
        );
        setStatus({ severity: 'success', text: 'Moved successfully.' });
      }
    } catch (err) {
      console.error(err);
      setStatus({ severity: 'error', text: err?.message || String(err) });
    } finally {
      setSubmitting(false);
    }
  };

  // Small helpers
  const L = ({ children }) => (
    <Typography sx={{ fontSize: '0.8rem', color: '#666', mb: 0.5 }}>{children}</Typography>
  );
  const field = (props) => <TextField size="small" fullWidth {...props} />;

  // Mode-specific content
  const renderContent = () => {
    if (isChangeAll) {
      return (
        <Box display="grid" gridTemplateColumns="1fr 1fr" gap={1}>
          <div style={{ gridColumn: 'span 1' }}>
            <L>Client</L>
            {field({
              value: draft.client,
              onChange: (e) => setDraft((d) => ({ ...d, client: e.target.value })),
            })}
          </div>
          <div style={{ gridColumn: 'span 1' }}>
            <L>Template Sys/Prin</L>
            {field({
              value: draft.templateSysPrin,
              onChange: (e) => setDraft((d) => ({ ...d, templateSysPrin: e.target.value })),
            })}
          </div>
          <Box gridColumn="span 2" mt={0.5}>
            <Typography fontSize="0.8rem" color="text.secondary">
              This will copy the template’s settings to all other Sys/Prins under the same client.
            </Typography>
          </Box>
        </Box>
      );
    }

    if (isEdit) {
      return (
        <Box display="grid" gridTemplateColumns="1fr 1fr" gap={1}>
          <div>
            <L>Client</L>
            {field({
              value: draft.client,
              onChange: (e) => setDraft((d) => ({ ...d, client: e.target.value })),
            })}
          </div>
          <div>
            <L>Sys/Prin</L>
            {field({
              value: draft.sysPrin,
              onChange: (e) => setDraft((d) => ({ ...d, sysPrin: e.target.value })),
            })}
          </div>

          <div>
            <L>Active</L>
            <FormControl fullWidth size="small">
              <Select
                value={draft.active}
                onChange={(e) => setDraft((d) => ({ ...d, active: e.target.value }))}
                displayEmpty
                sx={{ '.MuiSelect-select': { fontSize: '0.9rem' } }}
              >
                <MenuItem value="">(not set)</MenuItem>
                <MenuItem value="true">Active</MenuItem>
                <MenuItem value="false">Inactive</MenuItem>
              </Select>
            </FormControl>
          </div>

          <div>
            <L>Notes</L>
            {field({
              value: draft.notes,
              onChange: (e) => setDraft((d) => ({ ...d, notes: e.target.value })),
            })}
          </div>
        </Box>
      );
    }

    if (isNew) {
      return (
        <Box display="grid" gridTemplateColumns="1fr 1fr" gap={1}>
          <div>
            <L>Client</L>
            {field({
              value: draft.client,
              onChange: (e) => setDraft((d) => ({ ...d, client: e.target.value })),
            })}
          </div>
          <div>
            <L>Sys/Prin</L>
            {field({
              value: draft.sysPrin,
              onChange: (e) => setDraft((d) => ({ ...d, sysPrin: e.target.value })),
            })}
          </div>

          <div>
            <L>Active</L>
            <FormControl fullWidth size="small">
              <Select
                value={draft.active}
                onChange={(e) => setDraft((d) => ({ ...d, active: e.target.value }))}
                displayEmpty
                sx={{ '.MuiSelect-select': { fontSize: '0.9rem' } }}
              >
                <MenuItem value="">(not set)</MenuItem>
                <MenuItem value="true">Active</MenuItem>
                <MenuItem value="false">Inactive</MenuItem>
              </Select>
            </FormControl>
          </div>

          <div>
            <L>Notes</L>
            {field({
              value: draft.notes,
              onChange: (e) => setDraft((d) => ({ ...d, notes: e.target.value })),
            })}
          </div>
        </Box>
      );
    }

    if (isDuplicate) {
      return (
        <Box display="grid" gridTemplateColumns="1fr 1fr" gap={1}>
          <div>
            <L>Client</L>
            {field({
              value: draft.client,
              onChange: (e) => setDraft((d) => ({ ...d, client: e.target.value })),
            })}
          </div>
          <div>
            <L>Source Sys/Prin</L>
            {field({
              value: draft.sourceSysPrin,
              onChange: (e) => setDraft((d) => ({ ...d, sourceSysPrin: e.target.value })),
            })}
          </div>
          <div>
            <L>Target Sys/Prin</L>
            {field({
              value: draft.targetSysPrin,
              onChange: (e) => setDraft((d) => ({ ...d, targetSysPrin: e.target.value })),
            })}
          </div>

          <div>
            <L>Copy Areas</L>
            <FormControl fullWidth size="small">
              <InputLabel id="copy-areas-label">Copy Areas</InputLabel>
              <Select
                labelId="copy-areas-label"
                label="Copy Areas"
                value={draft.copyAreas}
                onChange={(e) => setDraft((d) => ({ ...d, copyAreas: e.target.value }))}
              >
                <MenuItem value="true">Yes</MenuItem>
                <MenuItem value="false">No</MenuItem>
              </Select>
            </FormControl>
          </div>

          <div>
            <L>Overwrite if Target Exists</L>
            <FormControl fullWidth size="small">
              <InputLabel id="overwrite-label">Overwrite</InputLabel>
              <Select
                labelId="overwrite-label"
                label="Overwrite"
                value={draft.overwrite}
                onChange={(e) => setDraft((d) => ({ ...d, overwrite: e.target.value }))}
              >
                <MenuItem value="false">No</MenuItem>
                <MenuItem value="true">Yes</MenuItem>
              </Select>
            </FormControl>
          </div>
        </Box>
      );
    }

    if (isMove) {
      return (
        <Box display="grid" gridTemplateColumns="1fr 1fr" gap={1}>
          <div>
            <L>Old Client</L>
            {field({
              value: draft.oldClient,
              onChange: (e) => setDraft((d) => ({ ...d, oldClient: e.target.value })),
            })}
          </div>
          <div>
            <L>New Client</L>
            {field({
              value: draft.newClient,
              onChange: (e) => setDraft((d) => ({ ...d, newClient: e.target.value })),
            })}
          </div>
          <div>
            <L>Sys/Prin</L>
            {field({
              value: draft.sysPrin,
              onChange: (e) => setDraft((d) => ({ ...d, sysPrin: e.target.value })),
            })}
          </div>
          <Box gridColumn="span 2" mt={0.5}>
            <Typography fontSize="0.8rem" color="text.secondary">
              Move will reassign this Sys/Prin from Old Client to New Client (keys change).
            </Typography>
          </Box>
        </Box>
      );
    }

    return null;
  };

  const primaryLabel = isChangeAll
    ? 'Apply'
    : isEdit
    ? 'Save'
    : isNew
    ? 'Create'
    : isDuplicate
    ? 'Duplicate'
    : isMove
    ? 'Move'
    : 'OK';

  return (
    <Dialog open={open} onClose={onClose} maxWidth="sm" fullWidth>
      {/* Blue header */}
      <DialogTitle
        sx={{
          bgcolor: 'primary.main',
          color: 'primary.contrastText',
          py: 1,
        }}
      >
        <span style={{ fontSize: '0.9rem', fontWeight: 500 }}>{title}</span>
      </DialogTitle>
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

        {renderContent()}
      </DialogContent>

      <DialogActions>
        <Button
          onClick={onClose}
          variant="outlined"
          size="small"
          disabled={submitting}
          sx={{ textTransform: 'none' }}
        >
          Close
        </Button>
        <Button
          onClick={handleSubmit}
          variant="contained"
          size="small"
          disabled={submitting}
          sx={{ textTransform: 'none' }}
        >
          {submitting ? 'Working…' : primaryLabel}
        </Button>
      </DialogActions>
    </Dialog>
  );
};

export default SysPrinInformationWindow;




