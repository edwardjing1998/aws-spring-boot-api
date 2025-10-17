// EditAtmCashPrefix.jsx
import React, { useState } from 'react';
import {
  TextField,
  FormControl,
  Select,
  MenuItem,
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
  const sysPrinsPrefixes = selectedGroupRow.sysPrinsPrefixes || [];

  const [selectedPrefix, setSelectedPrefix] = useState('');
  const [editablePrefix, setEditablePrefix] = useState('');
  const [editableAtmCashRule, setEditableAtmCashRule] = useState('');
  const [page, setPage] = useState(0);

  // Window state (handles detail/edit/delete)
  const [winOpen, setWinOpen] = useState(false);
  const [winMode, setWinMode] = useState('detail'); // 'detail' | 'edit' | 'delete'
  const [winRow, setWinRow] = useState(null);

  const pageCount = Math.ceil((sysPrinsPrefixes.length || 0) / PAGE_SIZE) || 0;
  const pageData = sysPrinsPrefixes.slice(page * PAGE_SIZE, (page + 1) * PAGE_SIZE);

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

  // Row selection (for inline edit area)
  const handleRowClick = (item) => {
    setSelectedPrefix(item.prefix);
    setEditablePrefix(item.prefix);
    setEditableAtmCashRule(item.atmCashRule);
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

  // Window confirm actions (plug your API here)
  const handleWindowConfirm = async (mode, draft) => {
    // mode: 'edit' | 'delete'
    // draft: latest values from the window (for edit mode)
    try {
      if (mode === 'edit') {
        // TODO: call your UPDATE endpoint with `draft`
        // await fetch('/api/prefixes/update', { method:'POST', body: JSON.stringify(draft), headers: {'Content-Type':'application/json'} });
        console.log('UPDATE payload:', draft);
        alert('Prefix updated (mock). Wire your API here.');
      } else if (mode === 'delete') {
        // TODO: call your DELETE endpoint with winRow
        // await fetch(`/api/prefixes/${winRow.prefix}`, { method:'DELETE' });
        console.log('DELETE key:', winRow?.prefix);
        alert('Prefix deleted (mock). Wire your API here.');
      }
      setWinOpen(false);
      // Optionally refresh table data here
    } catch (err) {
      console.error(err);
      alert(`Operation failed: ${err.message}`);
    }
  };

  // Inline add handler
  const handleAdd = async () => {
    if (!editablePrefix || editableAtmCashRule === '') {
      alert('Please enter both Account Prefix and ATM/Cash Rule');
      return;
    }
    const payload = {
      billingSp: selectedGroupRow?.billingSp || '',
      prefix: editablePrefix,
      atmCashRule: editableAtmCashRule,
    };
    try {
      const res = await fetch('http://localhost:4444/api/prefixes/add', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(payload),
      });
      if (!res.ok) throw new Error('Failed to add prefix');
      alert('Prefix added successfully');
      // Optionally refresh table data here
    } catch (err) {
      console.error(err);
      alert(`Error adding prefix: ${err.message}`);
    }
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
                        <React.Fragment key={`${item.prefix}-${index}`}>
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

                  {/* Pager (pulled up) */}
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
                      Page {sysPrinsPrefixes.length > 0 ? page + 1 : 0} of {sysPrinsPrefixes.length > 0 ? pageCount : 0}
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
                </div>
              </div>
            </div>
          </div>
        </CCol>
      </CRow>

      {/* Inline Edit form */}
      <CRow className="mb-3">
        <CCol xs={6}>
          <div style={{ fontSize: '0.75rem', fontWeight: 500, marginBottom: '2px', marginLeft: '2px' }}>
            Account Prefix
          </div>
          <TextField
            variant="outlined"
            fullWidth
            size="small"
            value={editablePrefix}
            onChange={(e) => setEditablePrefix(e.target.value)}
            sx={{
              backgroundColor: 'white',
              mb: 2,
              height: '44px',
              '& .MuiInputBase-input': {
                fontSize: '0.75rem',
                fontWeight: 500,
                paddingTop: '12px',
                paddingBottom: '12px',
              },
            }}
          />
        </CCol>

        <CCol xs={6}>
          <FormControl fullWidth size="small" sx={{ backgroundColor: 'white' }}>
            <div style={{ fontSize: '0.75rem', fontWeight: 500, marginBottom: '2px', marginLeft: '2px' }}>
              ATM/Cash
            </div>
            <Select
              labelId="atm-cash-label"
              id="atm-cash-prefix"
              value={editableAtmCashRule}
              onChange={(e) => setEditableAtmCashRule(e.target.value)}
              sx={{
                '.MuiSelect-select': {
                  fontWeight: 500,
                  fontSize: '0.78rem',
                },
              }}
            >
              <MenuItem value="" sx={{ fontSize: '0.78rem', fontWeight: 500 }}>
                Select Rule
              </MenuItem>
              <MenuItem value="0" sx={{ fontSize: '0.78rem', fontWeight: 500 }}>
                Destroy
              </MenuItem>
              <MenuItem value="1" sx={{ fontSize: '0.78rem', fontWeight: 500 }}>
                Return
              </MenuItem>
            </Select>
          </FormControl>
        </CCol>
      </CRow>

      {/* Save / Add controls */}
      <CRow>
        <CCol xs={12} className="d-flex gap-2">
          <Button
            variant="outlined"
            size="small"
            sx={{ fontSize: '0.78rem', textTransform: 'none' }}
            onClick={handleAdd}
          >
            Add / Save
          </Button>
        </CCol>
      </CRow>

      {/* Unified Window */}
      <AtmCashPrefixDetailWindow
        open={winOpen}
        mode={winMode}
        row={winRow}
        onClose={() => setWinOpen(false)}
        onConfirm={handleWindowConfirm}
      />
    </>
  );
};

export default EditAtmCashPrefix;






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
} from '@mui/material';

const atmCashLabel = (rule) => {
  if (rule === '0' || rule === 0) return 'Destroy';
  if (rule === '1' || rule === 1) return 'Return';
  return 'N/A';
};

/**
 * Props:
 * - open: boolean
 * - mode: 'detail' | 'edit' | 'delete'
 * - row: { billingSp, prefix, atmCashRule } | null
 * - onClose: () => void
 * - onConfirm?: (mode, draftRow) => void   // used for edit/delete confirm
 */
const AtmCashPrefixDetailWindow = ({ open, mode = 'detail', row, onClose, onConfirm }) => {
  const title = useMemo(() => {
    if (mode === 'edit') return 'Edit Prefix';
    if (mode === 'delete') return 'Delete Prefix';
    return 'Prefix Detail';
  }, [mode]);

  // Local draft for edit mode
  const [draft, setDraft] = useState({ billingSp: '', prefix: '', atmCashRule: '' });

  useEffect(() => {
    if (row) {
      setDraft({
        billingSp: row.billingSp ?? '',
        prefix: row.prefix ?? '',
        atmCashRule: String(row.atmCashRule ?? ''),
      });
    }
  }, [row, open]);

  const isDetail = mode === 'detail';
  const isEdit = mode === 'edit';
  const isDelete = mode === 'delete';

  const handleConfirm = () => {
    if (onConfirm) onConfirm(mode, draft);
  };

  return (
    <Dialog open={open} onClose={onClose} maxWidth="xs" fullWidth>
      <DialogTitle sx={{ fontSize: '0.95rem' }}>{title}</DialogTitle>
      <Divider />
      <DialogContent dividers sx={{ pt: 1 }}>
        {!row ? (
          <Typography sx={{ fontSize: '0.9rem' }}>(No data)</Typography>
        ) : isDetail ? (
          // DETAIL VIEW
          <Box display="grid" gridTemplateColumns="1fr 1fr" gap={1}>
            <Typography sx={{ fontSize: '0.8rem', color: '#666' }}>Billing SP</Typography>
            <Typography sx={{ fontSize: '0.9rem' }}>{row.billingSp ?? '(empty)'}</Typography>

            <Typography sx={{ fontSize: '0.8rem', color: '#666' }}>Prefix</Typography>
            <Typography sx={{ fontSize: '0.9rem' }}>{row.prefix ?? '(empty)'}</Typography>

            <Typography sx={{ fontSize: '0.8rem', color: '#666' }}>ATM/Cash</Typography>
            <Typography sx={{ fontSize: '0.9rem' }}>{atmCashLabel(row.atmCashRule)}</Typography>
          </Box>
        ) : isEdit ? (
          // EDIT FORM
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
              <Typography sx={{ fontSize: '0.9rem' }}>{row.billingSp ?? '(empty)'}</Typography>

              <Typography sx={{ fontSize: '0.8rem', color: '#666' }}>Prefix</Typography>
              <Typography sx={{ fontSize: '0.9rem' }}>{row.prefix ?? '(empty)'}</Typography>

              <Typography sx={{ fontSize: '0.8rem', color: '#666' }}>ATM/Cash</Typography>
              <Typography sx={{ fontSize: '0.9rem' }}>{atmCashLabel(row.atmCashRule)}</Typography>
            </Box>
          </Box>
        )}
      </DialogContent>

      <DialogActions>
        <Button onClick={onClose} variant="outlined" size="small" sx={{ textTransform: 'none' }}>
          {isDelete ? 'Cancel' : 'Close'}
        </Button>

        {isEdit && (
          <Button onClick={handleConfirm} variant="contained" size="small" sx={{ textTransform: 'none' }}>
            Save
          </Button>
        )}

        {isDelete && (
          <Button onClick={handleConfirm} color="error" variant="contained" size="small" sx={{ textTransform: 'none' }}>
            Delete
          </Button>
        )}
      </DialogActions>
    </Dialog>
  );
};

export default AtmCashPrefixDetailWindow;





