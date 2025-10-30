import React, { useState } from 'react';
import {
  Box,
  TextField,
  FormControl,
  Select,
  MenuItem,
  FormControlLabel,
  Checkbox,
} from '@mui/material';
import { CRow, CCol, CButton } from '@coreui/react';

  const EditClientInformation = ({
    selectedGroupRow,
    isEditable,
    setSelectedGroupRow,
    mode,              // from parent
    onClientUpdated,   // ✅ new: bubble up to parent
  }) => {

  const [updating, setUpdating] = useState(false);

  const handleChange = (field) => (e) => {
    setSelectedGroupRow((prev) => ({ ...prev, [field]: e.target.value }));
  };

  const handleCheckboxChange = (field) => (e) => {
    setSelectedGroupRow((prev) => ({ ...prev, [field]: e.target.checked }));
  };

  const handleUpdate = async () => {
    if (!selectedGroupRow?.client) {
      alert('Client ID is required.');
      return;
    }

    const {
      client,
      name,
      addr,
      city,
      state,
      zip,
      contact,
      phone,
      active,
      faxNumber,
      billingSp,
      reportBreakFlag,
      chLookUpType,
      excludeFromReport,
      positiveReports,
      subClientInd,
      subClientXref,
      amexIssued,
    } = selectedGroupRow || {};

    const payload = {
      client,
      name,
      addr,
      city,
      state,
      zip,
      contact,
      phone,
      active,
      faxNumber,
      billingSp,
      reportBreakFlag:
        typeof reportBreakFlag === 'string' ? Number(reportBreakFlag) : reportBreakFlag,
      chLookUpType:
        typeof chLookUpType === 'string' ? Number(chLookUpType) : chLookUpType,
      excludeFromReport: !!excludeFromReport,
      positiveReports: !!positiveReports,
      subClientInd: !!subClientInd,
      subClientXref,
      amexIssued: !!amexIssued,
    };

        try {
          setUpdating(true);
          const response = await fetch(
            'http://localhost:8089/client-sysprin-writer/api/client/update',
            {
              method: 'POST',
              headers: { 'Content-Type': 'application/json' },
              body: JSON.stringify(payload),
            }
          );

          if (!response.ok) {
            const text = await response.text();
            throw new Error(`Save failed: ${response.status} ${text}`);
          }
        // ✅ Normalize response (server might return JSON or plain text, and may be "thin")
        const ct = response.headers.get('content-type') || '';
        const savedRaw = ct.includes('application/json') ? await response.json() : await response.text();
        const saved = (savedRaw && typeof savedRaw === 'object')
          ? savedRaw
          : { ...payload, _message: typeof savedRaw === 'string' ? savedRaw : undefined };

        // ✅ keep local state in sync for the modal
        setSelectedGroupRow(prev => ({ ...(prev ?? {}), ...(saved ?? {}) }));

        // ✅ bubble to parent so list + preview update
        onClientUpdated?.(saved);

        console.log('Client updated successfully:', saved);
        } catch (err) {
          console.error(err);
          alert('An error occurred while saving. Check console for details.');
        } finally {
          setUpdating(false);
        }
      };

  const sharedSx = {
    '& .MuiInputBase-root': { height: '30px', fontSize: '0.78rem' },
    '& .MuiInputBase-input': {
      padding: '4px 4px',
      height: '30px',
      fontSize: '0.78rem',
      lineHeight: '1rem',
    },
    '& .MuiInputLabel-root': { fontSize: '0.78rem', lineHeight: '1rem' },
    '& .MuiInputBase-input.Mui-disabled': { color: 'black', WebkitTextFillColor: 'black' },
    '& .MuiInputLabel-root.Mui-disabled': { color: 'black' },
  };

  return (
    <div style={{ padding: '12px' }}>
      <CRow style={{ marginBottom: '20px' }}>
        {/* Address Line */}
        <CCol xs="6">
          <FormControl fullWidth>
            <label style={{ fontSize: '0.78rem', marginBottom: '4px' }}>
              Address Line
            </label>
            <TextField
              label=""
              value={selectedGroupRow.addr || ''}
              onChange={handleChange('addr')}
              size="small"
              fullWidth
              disabled={!isEditable}
              sx={sharedSx}
            />
          </FormControl>
        </CCol>

        {/* City */}
        <CCol xs="2">
          <FormControl fullWidth>
            <label style={{ fontSize: '0.78rem', marginBottom: '4px' }}>City</label>
            <TextField
              label=""
              value={selectedGroupRow.city || ''}
              onChange={handleChange('city')}
              size="small"
              fullWidth
              disabled={!isEditable}
              sx={sharedSx}
            />
          </FormControl>
        </CCol>

        {/* State */}
        <CCol xs="2">
          <FormControl fullWidth>
            <label style={{ fontSize: '0.78rem', marginBottom: '4px' }}>State</label>
            <TextField
              label=""
              value={selectedGroupRow.state || ''}
              onChange={handleChange('state')}
              size="small"
              fullWidth
              disabled={!isEditable}
              sx={sharedSx}
            />
          </FormControl>
        </CCol>

        {/* Zip */}
        <CCol xs="2">
          <FormControl fullWidth>
            <label style={{ fontSize: '0.78rem', marginBottom: '4px' }}>Zip Code</label>
            <TextField
              value={selectedGroupRow.zip || ''}
              onChange={handleChange('zip')}
              size="small"
              fullWidth
              disabled={!isEditable}
              sx={sharedSx}
              variant="outlined"
              label=""
            />
          </FormControl>
        </CCol>
      </CRow>

      {/* Contact / Phone / Fax */}
      <CRow style={{ marginBottom: '20px' }}>
        <CCol xs="4">
          <FormControl fullWidth>
            <label style={{ fontSize: '0.78rem', marginBottom: '4px' }}>Contact</label>
            <TextField
              label=""
              value={selectedGroupRow.contact || ''}
              onChange={handleChange('contact')}
              size="small"
              fullWidth
              disabled={!isEditable}
              sx={sharedSx}
            />
          </FormControl>
        </CCol>

        <CCol xs="4">
          <FormControl fullWidth>
            <label style={{ fontSize: '0.78rem', marginBottom: '4px' }}>Phone</label>
            <Box sx={{ display: 'flex', gap: 1 }}>
              {/* Country Code */}
              <FormControl sx={{ minWidth: 70 }} size="small">
                <Select
                  value={selectedGroupRow.phoneCountryCode || '+1'}
                  onChange={handleChange('phoneCountryCode')}
                  disabled={!isEditable}
                  displayEmpty
                  sx={{
                    ...sharedSx,
                    width: 70,
                    '& .MuiInputBase-root': {
                      height: '36px',
                      fontSize: '0.78rem',
                    },
                    '& .MuiSelect-select': {
                      paddingY: '6px',
                      fontSize: '0.78rem',
                    },
                  }}
                >
                  <MenuItem value="+1">🇺🇸 US</MenuItem>
                  <MenuItem value="+1-CA">🇨🇦 CA</MenuItem>
                  <MenuItem value="+44">🇬🇧 UK</MenuItem>
                  <MenuItem value="+61">🇦🇺 AU</MenuItem>
                  <MenuItem value="+91">🇮🇳 IN</MenuItem>
                </Select>
              </FormControl>

              {/* Number */}
              <Box sx={{ display: 'flex', gap: 1, alignItems: 'flex-start', flex: 1 }}>
                <TextField
                  label=""
                  value={selectedGroupRow.phone || ''}
                  onChange={handleChange('phone')}
                  size="small"
                  fullWidth
                  disabled={!isEditable}
                  sx={sharedSx}
                />
              </Box>
            </Box>
          </FormControl>
        </CCol>

        <CCol xs="4">
          <FormControl fullWidth>
            <label style={{ fontSize: '0.78rem', marginBottom: '4px' }}>Fax Number</label>
            <Box sx={{ display: 'flex', gap: 1 }}>
              {/* Country Code */}
              <FormControl sx={{ minWidth: 70 }} size="small">
                <Select
                  value={selectedGroupRow.phoneCountryCode || '+1'}
                  onChange={handleChange('phoneCountryCode')}
                  disabled={!isEditable}
                  displayEmpty
                  sx={{
                    ...sharedSx,
                    width: 70,
                    '& .MuiInputBase-root': {
                      height: '36px',
                      fontSize: '0.78rem',
                    },
                    '& .MuiSelect-select': {
                      paddingY: '6px',
                      fontSize: '0.78rem',
                    },
                  }}
                >
                  <MenuItem value="+1">🇺🇸 US</MenuItem>
                  <MenuItem value="+1-CA">🇨🇦 CA</MenuItem>
                  <MenuItem value="+44">🇬🇧 UK</MenuItem>
                  <MenuItem value="+61">🇦🇺 AU</MenuItem>
                  <MenuItem value="+91">🇮🇳 IN</MenuItem>
                </Select>
              </FormControl>

              {/* Number */}
              <Box sx={{ display: 'flex', gap: 1, alignItems: 'flex-start', flex: 1 }}>
                <TextField
                  label=""
                  value={selectedGroupRow.faxNumber || ''}
                  onChange={handleChange('faxNumber')}
                  size="small"
                  fullWidth
                  disabled={!isEditable}
                  sx={sharedSx}
                />
              </Box>
            </Box>
          </FormControl>
        </CCol>
      </CRow>

      {/* Billing / Report Breaks / Search Type */}
      <CRow style={{ marginBottom: '20px' }}>
        <CCol xs="4">
          <FormControl fullWidth>
            <label style={{ fontSize: '0.78rem', marginBottom: '4px' }}>
              Billing Sys/Prin
            </label>
            <TextField
              label=""
              value={selectedGroupRow.billingSp || ''}
              onChange={handleChange('billingSp')}
              size="small"
              fullWidth
              disabled={!isEditable}
              sx={sharedSx}
            />
          </FormControl>
        </CCol>

        <CCol xs="4">
          <FormControl fullWidth size="small" sx={sharedSx}>
            <label style={{ fontSize: '0.78rem', marginBottom: '4px' }}>
              Report Breaks
            </label>
            <Select
              value={selectedGroupRow.reportBreakFlag?.toString() || ''}
              onChange={handleChange('reportBreakFlag')}
              disabled={!isEditable}
              sx={{
                ...sharedSx,
                '& .MuiSelect-select': {
                  display: 'flex',
                  alignItems: 'center',
                  lineHeight: '1rem',
                  fontSize: '0.78rem',
                  minHeight: '36px',
                },
                '& .MuiInputBase-root': { height: '36px' },
              }}
            >
              <MenuItem value="0" sx={{ fontSize: '0.78rem' }}>
                None
              </MenuItem>
              <MenuItem value="1" sx={{ fontSize: '0.78rem' }}>
                System
              </MenuItem>
              <MenuItem value="2" sx={{ fontSize: '0.78rem' }}>
                Sys/Prin
              </MenuItem>
            </Select>
          </FormControl>
        </CCol>

        <CCol xs="4">
          <FormControl fullWidth size="small" sx={sharedSx}>
            <label style={{ fontSize: '0.78rem', marginBottom: '4px' }}>
              Search Type
            </label>
            <Select
              value={selectedGroupRow.chLookUpType?.toString() || ''}
              onChange={handleChange('chLookUpType')}
              disabled={!isEditable}
              sx={{
                ...sharedSx,
                '& .MuiSelect-select': {
                  display: 'flex',
                  alignItems: 'center',
                  lineHeight: '1rem',
                  fontSize: '0.78rem',
                  minHeight: '36px',
                },
                '& .MuiInputBase-root': { height: '36px' },
              }}
            >
              <MenuItem value="" sx={{ fontSize: '0.78rem' }}>
                None
              </MenuItem>
              <MenuItem value="0" sx={{ fontSize: '0.78rem' }}>
                Standard
              </MenuItem>
              <MenuItem value="1" sx={{ fontSize: '0.78rem' }}>
                Amex-AS400
              </MenuItem>
              <MenuItem value="2" sx={{ fontSize: '0.78rem' }}>
                AS400
              </MenuItem>
            </Select>
          </FormControl>
        </CCol>
      </CRow>

      {/* Checkboxes */}
      <CRow style={{ marginBottom: '20px' }}>
        <CCol xs="3">
          <FormControlLabel
            control={
              <Checkbox
                size="small"
                checked={!!selectedGroupRow.active}
                onChange={handleCheckboxChange('active')}
                disabled={!isEditable}
              />
            }
            label={<span style={{ fontSize: '0.78rem' }}>Client Active</span>}
          />
        </CCol>
        <CCol xs="3">
          <FormControlLabel
            control={
              <Checkbox
                size="small"
                checked={!!selectedGroupRow.positiveReports}
                onChange={handleCheckboxChange('positiveReports')}
                disabled={!isEditable}
              />
            }
            label={<span style={{ fontSize: '0.78rem' }}>Positive Reporting</span>}
          />
        </CCol>
        <CCol xs="2">
          <FormControlLabel
            control={
              <Checkbox
                size="small"
                checked={!!selectedGroupRow.subClientInd}
                onChange={handleCheckboxChange('subClientInd')}
                disabled={!isEditable}
              />
            }
            label={<span style={{ fontSize: '0.78rem' }}>Sub Client</span>}
          />
        </CCol>
        <CCol xs="5">
          <FormControlLabel
            control={
              <Checkbox
                size="small"
                checked={!!selectedGroupRow.excludeFromReport}
                onChange={handleCheckboxChange('excludeFromReport')}
                disabled={!isEditable}
              />
            }
            label={
              <span style={{ fontSize: '0.78rem' }}>Exclude From Postage Reports</span>
            }
          />
        </CCol>
      </CRow>

      <CRow className="mt-4" style={{ alignItems: 'center' }}>
        {/* AMEX box */}
        <CCol xs="8">
          <Box
            sx={{
              padding: '6px 8px',
              backgroundColor: '#e9f2f8',
              borderRadius: '4px',
              display: 'flex',
              alignItems: 'center',
              gap: 1,
              width: '100%',
            }}
          >
            <Box
              sx={{
                width: 40,
                height: 40,
                backgroundColor: '#1976d2',
                color: 'white',
                fontWeight: 'bold',
                fontSize: '0.75rem',
                borderRadius: '4px',
                display: 'flex',
                alignItems: 'center',
                justifyContent: 'center',
                flexShrink: 0,
                mt: 0.5,
              }}
            >
              AMEX
            </Box>

            <Box sx={{ flex: 1 }}>
              <Box sx={{ fontSize: '0.78rem', fontWeight: 400, color: 'black', mb: '-2px' }}>
                ATTN American Express
              </Box>

              <FormControlLabel
                control={
                  <Checkbox
                    size="small"
                    checked={!!selectedGroupRow.amexIssued}
                    onChange={handleCheckboxChange('amexIssued')}
                    disabled={!isEditable}
                  />
                }
                label={
                  <span style={{ fontSize: '0.78rem' }}>
                    Click here If American Express Issued
                  </span>
                }
              />
            </Box>
          </Box>
        </CCol>

        {/* Update button — only visible in Edit mode */}
        {mode === 'edit' && (
          <CCol xs="4" className="d-flex justify-content-end">
            <CButton
              color="info"
              onClick={handleUpdate}
              style={{ fontSize: '0.78rem' }}
              disabled={updating}
            >
              {updating ? 'Updating…' : 'Update'}
            </CButton>
          </CCol>
        )}
      </CRow>
    </div>
  );
};

export default EditClientInformation;




import React, { useState, useEffect } from 'react';
import { Box, IconButton, Tabs, Tab, Button, TextField, Snackbar, Alert } from '@mui/material';
import CloseIcon from '@mui/icons-material/Close';
import { CRow, CCol } from '@coreui/react';

import EditClientInformation from '../EditClientInformation';
import EditAtmCashPrefix from '../EditAtmCashPrefix';
import EditClientReport from '../EditClientReport';
import EditClientEmailSetup from '../EditClientEmailSetup';

import { handleCreate, handleUpdate, handleDelete } from './ClientService';

const ClientInformationWindow = ({
  onClose,
  selectedGroupRow,
  setSelectedGroupRow,
  mode,
  // (optional) already in your previous flow
  onClientCreated,
  onClientUpdated,
  // NEW: bubble email changes up to the page
  onClientEmailsChanged,
}) => {
  const [tabIndex, setTabIndex] = useState(0);
  const [isEditable, setIsEditable] = useState(true);
  const [status, setStatus] = useState({ open: false, severity: 'info', message: '' });

  const makeEmptyClient = () => ({
    client: '', name: '', addr: '', city: '', state: '', zip: '',
    contact: '', phone: '', active: true, faxNumber: '', billingSp: '',
    reportBreakFlag: 0, chLookUpType: 0, excludeFromReport: false, positiveReports: false,
    subClientInd: false, subClientXref: '', amexIssued: false,
    clientEmail: [], // helpful default
  });

  useEffect(() => {
    if (mode === 'new') {
      setIsEditable(true);
      setSelectedGroupRow(prev => (prev && Object.keys(prev).length ? prev : makeEmptyClient()));
    } else if (mode === 'edit') {
      setIsEditable(true);
    } else {
      setIsEditable(false);
    }
  }, [mode, setSelectedGroupRow]);

  const viewRow = mode === 'new'
    ? (selectedGroupRow ?? makeEmptyClient())
    : (selectedGroupRow ?? makeEmptyClient());

  const sharedSx = {
    '& .MuiInputBase-root': { height: '30px', fontSize: '0.78rem' },
    '& .MuiInputBase-input': { padding: '4px 4px', height: '30px', fontSize: '0.78rem', lineHeight: '1rem' },
    '& .MuiInputLabel-root': { fontSize: '0.78rem', lineHeight: '1rem' },
    '& .MuiInputBase-input.Mui-disabled': { color: 'black', WebkitTextFillColor: 'black' },
    '& .MuiInputLabel-root.Mui-disabled': { color: 'black' },
  };

  const showStatus = (message, severity = 'success') =>
    setStatus({ open: true, severity, message });
  const handleStatusClose = (_e, reason) => {
    if (reason === 'clickaway') return;
    setStatus(prev => ({ ...prev, open: false }));
  };

  const onCreateClick = async () => {
    try {
      const saved = await handleCreate(viewRow);
      showStatus('Client created successfully', 'success');
      setSelectedGroupRow(prev => ({ ...(prev ?? {}), ...(saved ?? {}) }));
      onClientCreated?.(saved);
    } catch (err) {
      console.error(err);
      showStatus(err.message || 'An error occurred while creating.', 'error');
    }
  };

  const onUpdateClick = async () => {
    try {
      const saved = await handleUpdate(viewRow);
      showStatus('Client updated successfully', 'success');
      setSelectedGroupRow(prev => ({ ...(prev ?? {}), ...(saved ?? {}) }));
      onClientUpdated?.(saved);
    } catch (err) {
      console.error(err);
      showStatus(err.message || 'An error occurred while updating.', 'error');
    }
  };

  const onDeleteClick = async () => {
    if (!viewRow?.client) {
      showStatus('Client ID is required to delete.', 'warning');
      return;
    }
    if (!window.confirm(`Delete client "${viewRow.client}"? This cannot be undone.`)) return;
    try {
      await handleDelete(viewRow.client);
      showStatus('Client deleted successfully', 'success');
    } catch (err) {
      console.error(err);
      showStatus(err.message || 'An error occurred while deleting.', 'error');
    }
  };

  const onPrimaryClick = () => {
    if (mode === 'edit') return onUpdateClick();
    if (mode === 'delete') return onDeleteClick();
    return onCreateClick();
  };

  // NEW: when child changes emails, sync local state & bubble up
  const handleEmailsChanged = (clientId, nextEmailList) => {
    if (!clientId) return;
    setSelectedGroupRow(prev => ({ ...(prev ?? {}), client: clientId, clientEmail: Array.isArray(nextEmailList) ? nextEmailList : [] }));
    onClientEmailsChanged?.(clientId, nextEmailList);
  };

  const renderButtonRow = () => (
    <CRow className="mt-3" style={{ maxWidth: 900, margin: '0 auto' }}>
      <CCol style={{ display: 'flex', justifyContent: 'flex-start' }}>
        <Button variant="outlined" size="small" onClick={() => setTabIndex((p) => Math.max(p - 1, 0))} disabled={tabIndex === 0}>
          Back
        </Button>
      </CCol>
      <CCol style={{ display: 'flex', justifyContent: 'flex-end', gap: '12px' }}>
        <Button variant="contained" size="small" color={mode === 'delete' ? 'error' : 'primary'} onClick={onPrimaryClick}>
          {mode === 'new' ? 'Create' : mode === 'edit' ? 'Update' : mode === 'delete' ? 'Delete' : 'Save'}
        </Button>
        <Button variant="outlined" size="small" onClick={() => setTabIndex((p) => Math.min(p + 1, 3))} disabled={tabIndex === 3}>
          Next
        </Button>
      </CCol>
    </CRow>
  );

  const borderPanelStyle = { border: '1px solid #ccc', borderRadius: '4px', padding: '8px', height: '440px', maxWidth: '900px', width: '100%', margin: '0 auto' };
  const noBorderPanelStyle = { border: '0px solid transparent', borderRadius: '4px', padding: '8px', height: '440px', maxWidth: '900px', width: '100%', margin: '0 auto' };

  return (
    <>
      <Box sx={{ display: 'flex', justifyContent: 'space-between', alignItems: 'center', backgroundColor: '#1976d2', color: 'white', height: 40, mx: -2, mt: -2, px: 0, py: 0, borderTopLeftRadius: 8, borderTopRightRadius: 8 }}>
        <Box sx={{ fontWeight: 600, fontSize: '0.95rem', lineHeight: '40px', pl: 2 }}>Client Information</Box>
        <IconButton onClick={onClose} size="small" sx={{ color: 'white', mr: 1 }}>
          <CloseIcon fontSize="small" />
        </IconButton>
      </Box>

      <Box sx={{ p: 2, height: 710, overflow: 'hidden', display: 'flex', flexDirection: 'column' }}>
        {/* Header inline inputs */}
        <Box sx={{ pb: 1, maxWidth: 900, mx: 'auto', width: '100%' }}>
          <CRow style={{ marginBottom: '12px', marginTop: 0 }}>
            <CCol xs="6">
              <Box sx={{ display: 'flex', alignItems: 'center', gap: 1 }}>
                <Box component="label" sx={{ fontSize: '0.78rem', minWidth: 72 }}>Client ID</Box>
                <TextField
                  label=""
                  value={viewRow.client ?? ''}
                  size="small"
                  disabled={!isEditable}
                  sx={{ ...sharedSx, minWidth: 160, flex: 1 }}
                  onChange={(e) =>
                    setSelectedGroupRow(prev => ({ ...(prev ?? makeEmptyClient()), client: e.target.value }))
                  }
                />
              </Box>
            </CCol>
            <CCol xs="6">
              <Box sx={{ display: 'flex', alignItems: 'center', gap: 1 }}>
                <Box component="label" sx={{ fontSize: '0.78rem', minWidth: 72 }}>Name</Box>
                <TextField
                  label=""
                  value={viewRow.name ?? ''}
                  size="small"
                  disabled={!isEditable}
                  sx={{ ...sharedSx, flex: 1 }}
                  onChange={(e) =>
                    setSelectedGroupRow(prev => ({ ...(prev ?? makeEmptyClient()), name: e.target.value }))
                  }
                />
              </Box>
            </CCol>
          </CRow>
        </Box>

        <Tabs value={tabIndex} onChange={(_e, v) => setTabIndex(v)} variant="fullWidth" sx={{ mt: -0.5, mb: 2 }} TabIndicatorProps={{ sx: { width: '30px', left: 'calc(50% - 15px)' } }}>
          <Tab label={<Box sx={{ display: 'flex', alignItems: 'center', gap: .5 }}><Box sx={{ width:18, height:18, borderRadius:'50%', backgroundColor:'#1976d2', color:'white', fontSize:'.7rem', display:'flex', alignItems:'center', justifyContent:'center' }}>1</Box>Client Information</Box>} sx={{ fontSize: '0.78rem', textTransform: 'none', minWidth: 205, maxWidth: 205, px: 1 }} />
          <Tab label={<Box sx={{ display: 'flex', alignItems: 'center', gap: .5 }}><Box sx={{ width:18, height:18, borderRadius:'50%', backgroundColor:'#1976d2', color:'white', fontSize:'.7rem', display:'flex', alignItems:'center', justifyContent:'center' }}>2</Box>Client Email Setup</Box>} sx={{ fontSize: '0.78rem', textTransform: 'none', minWidth: 160, maxWidth: 170, px: 1 }} />
          <Tab label={<Box sx={{ display: 'flex', alignItems: 'center', gap: .5 }}><Box sx={{ width:18, height:18, borderRadius:'50%', backgroundColor:'#1976d2', color:'white', fontSize:'.7rem', display:'flex', alignItems:'center', justifyContent:'center' }}>3</Box>Client Reports</Box>} sx={{ fontSize: '0.78rem', textTransform: 'none', minWidth: 135, maxWidth: 145, px: 1 }} />
          <Tab label={<Box sx={{ display: 'flex', alignItems: 'center', gap: .5 }}><Box sx={{ width:18, height:18, borderRadius:'50%', backgroundColor:'#1976d2', color:'white', fontSize:'.7rem', display:'flex', alignItems:'center', justifyContent:'center' }}>4</Box>Client ATM/Cash Prefixes</Box>} sx={{ fontSize: '0.78rem', textTransform: 'none', minWidth: 195, maxWidth: 205, px: 1 }} />
        </Tabs>

        <Box sx={{ flex: 1, overflow: 'auto' }}>
          {tabIndex === 0 && (
            <>
              <CRow className="mb-2" style={borderPanelStyle}>
                <CCol>
                  <div style={{ fontSize: '0.78rem', paddingTop: '12px', height: '100%' }}>
                    <EditClientInformation
                      selectedGroupRow={viewRow}
                      isEditable={isEditable}
                      setSelectedGroupRow={setSelectedGroupRow}
                      mode={mode}
                      onClientUpdated={onClientUpdated}
                    />
                  </div>
                </CCol>
              </CRow>
              {renderButtonRow()}
            </>
          )}

          {tabIndex === 1 && (
            <>
              <CRow className="mb-2" style={noBorderPanelStyle}>
                <CCol>
                  <div style={{ fontSize: '0.78rem', paddingTop: '12px', height: '100%' }}>
                    <EditClientEmailSetup
                      selectedGroupRow={viewRow}
                      isEditable={isEditable}
                      // NEW: pass handler down
                      onEmailsChanged={handleEmailsChanged}
                    />
                  </div>
                </CCol>
              </CRow>
              {renderButtonRow()}
            </>
          )}

          {tabIndex === 2 && (
            <>
              <CRow className="mb-2" style={noBorderPanelStyle}>
                <CCol>
                  <div style={{ fontSize: '0.78rem', paddingTop: '12px', height: '100%' }}>
                    <EditClientReport selectedGroupRow={viewRow} isEditable={isEditable} />
                  </div>
                </CCol>
              </CRow>
              {renderButtonRow()}
            </>
          )}

          {tabIndex === 3 && (
            <>
              <CRow className="mb-2" style={borderPanelStyle}>
                <CCol>
                  <div style={{ fontSize: '0.78rem', paddingTop: '12px', height: '80%' }}>
                    <EditAtmCashPrefix selectedGroupRow={viewRow} isEditable={isEditable} />
                  </div>
                </CCol>
              </CRow>
              {renderButtonRow()}
            </>
          )}
        </Box>
      </Box>

      <Snackbar open={status.open} autoHideDuration={3000} onClose={handleStatusClose} anchorOrigin={{ vertical: 'top', horizontal: 'center' }}>
        <Alert onClose={handleStatusClose} severity={status.severity} variant="filled" sx={{ width: '100%' }}>
          {status.message}
        </Alert>
      </Snackbar>
    </>
  );
};

export default ClientInformationWindow;


