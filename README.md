import React, { useState, useEffect } from 'react';
import {
  Box,
  TextField,
  FormControl,
  InputLabel,
  Select,
  MenuItem,
  Typography,
  IconButton,
  Button
} from '@mui/material';
import {
  CRow,
  CCol,
  CButton
} from '@coreui/react';
import AddIcon from '@mui/icons-material/Add';
import DeleteIcon from '@mui/icons-material/Delete';
import NoteAddIcon from '@mui/icons-material/NoteAdd';

const PAGE_SIZE = 10;

const EditAtmCashPrefix = ({ selectedGroupRow = {} }) => {
  const sysPrinsPrefixes = selectedGroupRow.sysPrinsPrefixes || [];

  const [selectedPrefix, setSelectedPrefix] = useState('');
  const [editablePrefix, setEditablePrefix] = useState('');
  const [editableAtmCashRule, setEditableAtmCashRule] = useState('');
  const [page, setPage] = useState(0);

  const pageCount = Math.ceil((sysPrinsPrefixes.length || 0) / PAGE_SIZE);
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
    cursor: 'pointer'
  });

  const headerStyle = {
    ...cellStyle(false),
    fontWeight: 'bold',
    backgroundColor: '#f0f0f0',
    borderBottom: '1px dotted #ccc',
    cursor: 'default'
  };

  const handleNew = () => {
    setEditablePrefix('');
    setEditableAtmCashRule('');
    setSelectedPrefix('');
    alert('New Prefix created');
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
      const response = await fetch('http://localhost:4444/api/prefixes/add', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(payload),
      });

      if (!response.ok) throw new Error('Failed to add prefix');

      alert('Prefix added successfully');
    } catch (error) {
      console.error('Error adding prefix:', error);
      alert(`Error adding prefix: ${error.message}`);
    }
  };

  const handleRemove = () => {
    if (!selectedPrefix) {
      alert('Please select a prefix to remove');
      return;
    }
    alert(`Prefix ${selectedPrefix} removed`);
  };

  const handleRowClick = (item) => {
    setSelectedPrefix(item.prefix);
    setEditablePrefix(item.prefix);
    setEditableAtmCashRule(item.atmCashRule);
  };

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
                      alignContent: 'start'
                    }}
                  >
                    <div style={headerStyle}>Billing SP</div>
                    <div style={headerStyle}>Prefix</div>
                    <div style={headerStyle}>ATM/Cash</div>
                    <div style={headerStyle}></div>
                    {pageData.map((item, index) => (
                      <React.Fragment key={`${item.prefix}-${index}`}>
                        <div style={cellStyle(item.prefix === selectedPrefix)} onClick={() => handleRowClick(item)}>{item.billingSp}</div>
                        <div style={cellStyle(item.prefix === selectedPrefix)} onClick={() => handleRowClick(item)}>{item.prefix}</div>
                        <div style={cellStyle(item.prefix === selectedPrefix)} onClick={() => handleRowClick(item)}>
                          {item.atmCashRule === '0' ? 'Destroy' : item.atmCashRule === '1' ? 'Return' : 'N/A'}
                        </div>
                        <div style={cellStyle(false)}>
                          <IconButton size="small" onClick={handleNew}><NoteAddIcon fontSize="small" /></IconButton>
                          <IconButton size="small" onClick={handleAdd}><AddIcon fontSize="small" /></IconButton>
                          <IconButton size="small" onClick={handleRemove}><DeleteIcon fontSize="small" /></IconButton>
                        </div>
                      </React.Fragment>
                    ))}
                  </div>
                  <div
                    style={{
                      marginTop: '16px',
                      display: 'flex',
                      justifyContent: 'space-between',
                      alignItems: 'center'
                    }}
                  >
                    <Button
                      variant="text"
                      size="small"
                      sx={{ fontSize: '0.7rem', padding: '2px 8px', minWidth: 'unset', textTransform: 'none' }}
                      onClick={() => setPage((p) => Math.max(p - 1, 0))}
                      disabled={page === 0}
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
                      onClick={() => setPage((p) => Math.min(p + 1, pageCount - 1))}
                      disabled={page === pageCount - 1}
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

      <CRow className="mb-3">
        <CCol xs={6}>
          <div style={{ fontSize: '0.75rem',  fontWeight: 500, marginBottom: '2px', marginLeft: '2px' }} >
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
                paddingBottom: '12px'
              }
            }}
            InputLabelProps={{
              sx: {
                fontSize: '0.78rem',
                fontWeight: 500
              }
            }}
          />
        </CCol>

        <CCol xs={6}>
          <FormControl fullWidth size="small" sx={{ backgroundColor: 'white' }}>
            <div style={{ fontSize: '0.75rem',  fontWeight: 500, marginBottom: '2px', marginLeft: '2px' }} >
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
                }
              }}
            >
              <MenuItem value="" sx={{ fontSize: '0.78rem', fontWeight: 500 }}>Select Rule</MenuItem>
              <MenuItem value="0" sx={{ fontSize: '0.78rem', fontWeight: 500 }}>Destroy</MenuItem>
              <MenuItem value="1" sx={{ fontSize: '0.78rem', fontWeight: 500 }}>Return</MenuItem>
            </Select>
          </FormControl>
        </CCol>
      </CRow>
    </>
  );
};

export default EditAtmCashPrefix;
