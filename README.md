import React, { useEffect, useMemo, useState } from 'react';
import {
  Dialog,
  DialogTitle,
  DialogContent,
  DialogActions,
  Button,
  Box,
  FormControl,
  InputLabel,
  Select,
  MenuItem,
  Typography,
  Alert,
  SelectChangeEvent,
} from '@mui/material';

import { createInvalidArea, deleteInvalidArea } from './EditInvalidedArea.service';

const STATES_50 = [
  'AL','AK','AZ','AR','CA','CO','CT','DE','FL','GA',
  'HI','ID','IL','IN','IA','KS','KY','LA','ME','MD',
  'MA','MI','MN','MS','MO','MT','NE','NV','NH','NJ',
  'NM','NY','NC','ND','OH','OK','OR','PA','RI','SC',
  'SD','TN','TX','UT','VT','VA','WA','WV','WI','WY'
];

export interface EditInvalidedAreaWindowProps {
  open: boolean;
  onClose: () => void;
  sysPrin: string;

  onCreated?: (areaCode: string) => void;
  onDeleted?: (areaCode: string) => void;

  mode?: 'create' | 'delete';
  initialArea?: string;
}

const EditInvalidedAreaWindow: React.FC<EditInvalidedAreaWindowProps> = ({
  open,
  onClose,
  sysPrin,
  onCreated,
  onDeleted,
  mode = 'create',
  initialArea = '',
}) => {
  const [area, setArea] = useState<string>(initialArea || '');
  const [submitting, setSubmitting] = useState<boolean>(false);
  const [successMsg, setSuccessMsg] = useState<string>('');
  const [errorMsg, setErrorMsg] = useState<string>('');

  const isDelete = mode === 'delete';

  // reset dialog state on open
  useEffect(() => {
    if (open) {
      setArea(initialArea || '');
      setSuccessMsg('');
      setErrorMsg('');
    }
  }, [open, initialArea]);

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
    } catch (e: any) {
      setErrorMsg(
        e?.message ||
        (isDelete ? 'Failed to delete area.' : 'Failed to create area.')
      );
    } finally {
      setSubmitting(false);
    }
  };

  return (
    <Dialog
      open={open}
      onClose={submitting ? undefined : onClose}
      maxWidth="xs"
      fullWidth
    >
      <DialogTitle sx={{ fontSize: '1rem' }}>{title}</DialogTitle>

      <DialogContent dividers>
        <Box sx={{ display: 'grid', gap: 2 }}>
          {successMsg && <Alert severity="success">{successMsg}</Alert>}
          {errorMsg && <Alert severity="error">{errorMsg}</Alert>}

          {isDelete ? (
            <>
              <Typography variant="body2" sx={{ color: 'text.secondary' }}>
                You are about to <strong>delete</strong> the area below from the
                "Do Not Deliver" list.
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
                  onChange={(e: SelectChangeEvent) => {
                    setArea(e.target.value);
                    setSuccessMsg('');
                    setErrorMsg('');
                  }}
                  disabled={submitting}
                >
                  {STATES_50.map((s) => (
                    <MenuItem key={s} value={s}>
                      {s}
                    </MenuItem>
                  ))}
                </Select>
              </FormControl>
            </>
          )}
        </Box>
      </DialogContent>

      <DialogActions>
        <Button onClick={onClose} disabled={submitting}>
          Close
        </Button>

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
};

export default EditInvalidedAreaWindow;
