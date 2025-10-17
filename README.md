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

// NEW: import the window
import AtmCashPrefixDetailWindow from './utils/AtmCashPrefixDetailWindow';

const PAGE_SIZE = 5;

const EditAtmCashPrefix = ({ selectedGroupRow = {} }) => {
  const sysPrinsPrefixes = selectedGroupRow.sysPrinsPrefixes || [];

  const [selectedPrefix, setSelectedPrefix] = useState('');
  const [editablePrefix, setEditablePrefix] = useState('');
  const [editableAtmCashRule, setEditableAtmCashRule] = useState('');
  const [page, setPage] = useState(0);

  // Detail dialog state
  const [detailOpen, setDetailOpen] = useState(false);
  const [detailRow, setDetailRow] = useState(null);

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

  // Row selection
  const handleRowClick = (item) => {
    setSelectedPrefix(item.prefix);
    setEditablePrefix(item.prefix);
    setEditableAtmCashRule(item.atmCashRule);
  };

  // Actions
  const handleDetail = (item, e) => {
    e?.stopPropagation();
    setSelectedPrefix(item.prefix);
    setEditablePrefix(item.prefix);
    setEditableAtmCashRule(item.atmCashRule);
    setDetailRow(item);
    setDetailOpen(true);
  };

  const handleEdit = (item, e) => {
    e?.stopPropagation();
    setSelectedPrefix(item.prefix);
    setEditablePrefix(item.prefix);
    setEditableAtmCashRule(item.atmCashRule);
    // If you have an Edit window, open it here similarly
  };

  const handleRemove = async (item, e) => {
    e?.stopPropagation();
    const prefix = item?.prefix ?? selectedPrefix;
    if (!prefix) {
      alert('Please select a prefix to remove');
      return;
    }
    // TODO: call your DELETE API
    alert(`Prefix ${prefix} removed`);
  };

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
                              <IconButton size="small" aria-label="detail" onClick={(e) => handleDetail(item, e)}>
                                <VisibilityIcon fontSize="small" />
                              </IconButton>
                            </Tooltip>
                            <Tooltip title="Edit">
                              <IconButton size="small" aria-label="edit" onClick={(e) => handleEdit(item, e)}>
                                <EditIcon fontSize="small" />
                              </IconButton>
                            </Tooltip>
                            <Tooltip title="Delete">
                              <IconButton size="small" aria-label="delete" onClick={(e) => handleRemove(item, e)}>
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
                      marginTop: '16px',
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

      {/* Edit form */}
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

      {/* Detail Window */}
      <AtmCashPrefixDetailWindow
        open={detailOpen}
        row={detailRow}
        onClose={() => setDetailOpen(false)}
      />
    </>
  );
};

export default EditAtmCashPrefix;
