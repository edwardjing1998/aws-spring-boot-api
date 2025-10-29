// ClientInformationWindow.jsx
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
  // ✅ NEW: callbacks so parent can update its clientList
  onClientCreated,
  onClientUpdated,
}) => {
  const [tabIndex, setTabIndex] = useState(0);
  const [isEditable, setIsEditable] = useState(true);

  // ---- helpers -------------------------------------------------------------
  const makeEmptyClient = () => ({
    client: '',
    name: '',
    addr: '',
    city: '',
    state: '',
    zip: '',
    contact: '',
    phone: '',
    active: true,
    faxNumber: '',
    billingSp: '',
    reportBreakFlag: 0,
    chLookUpType: 0,
    excludeFromReport: false,
    positiveReports: false,
    subClientInd: false,
    subClientXref: '',
    amexIssued: false,
  });

  // normalize payload for create/update
  const buildPayload = (row) => {
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
    } = row || {};

    return {
      client,
      name,
      addr,
      city,
      state,
      zip,
      contact,
      phone,
      active: !!active,
      faxNumber,
      billingSp,
      reportBreakFlag:
        typeof reportBreakFlag === 'string' ? Number(reportBreakFlag) : (reportBreakFlag ?? 0),
      chLookUpType:
        typeof chLookUpType === 'string' ? Number(chLookUpType) : (chLookUpType ?? 0),
      excludeFromReport: !!excludeFromReport,
      positiveReports: !!positiveReports,
      subClientInd: !!subClientInd,
      subClientXref,
      amexIssued: !!amexIssued,
    };
  };

  // ---- seed state by mode --------------------------------------------------
  useEffect(() => {
    if (mode === 'new') {
      setIsEditable(true);
      setSelectedGroupRow(prev =>
        prev && Object.keys(prev).length ? prev : makeEmptyClient()
      );
    } else if (mode === 'edit') {
      setIsEditable(true);
    } else {
      // view/delete
      setIsEditable(false);
    }
  }, [mode, setSelectedGroupRow]);

  const viewRow = mode === 'new'
    ? (selectedGroupRow ?? makeEmptyClient())
    : (selectedGroupRow ?? {});

  // ---- shared MUI sx -------------------------------------------------------
  const sharedSx = {
    '& .MuiInputBase-root': { height: '30px', fontSize: '0.78rem' },
    '& .MuiInputBase-input': { padding: '4px 4px', height: '30px', fontSize: '0.78rem', lineHeight: '1rem' },
    '& .MuiInputLabel-root': { fontSize: '0.78rem', lineHeight: '1rem' },
    '& .MuiInputBase-input.Mui-disabled': { color: 'black', WebkitTextFillColor: 'black' },
    '& .MuiInputLabel-root.Mui-disabled': { color: 'black' },
  };

  // ---- tab handlers --------------------------------------------------------
  const handleTabChange = (_e, newValue) => setTabIndex(newValue);
  const handleNext = () => setTabIndex(prev => Math.min(prev + 1, 3));
  const handleBack = () => setTabIndex(prev => Math.max(prev - 1, 0));

  // ---- status snackbar -----------------------------------------------------
  const [status, setStatus] = useState({ open: false, severity: 'info', message: '' });
  const showStatus = (message, severity = 'success') =>
    setStatus({ open: true, severity, message });
  const handleStatusClose = (_e, reason) => {
    if (reason === 'clickaway') return;
    setStatus(prev => ({ ...prev, open: false }));
  };

  // ---- actions -------------------------------------------------------------
  const onCreateClick = async () => {
    try {
      const payload = buildPayload(viewRow);
      const saved = await handleCreate(payload);
      console.log('Client created successfully:', saved);
      showStatus('Client created successfully', 'success');

      // keep local selection in sync
      setSelectedGroupRow(prev => ({ ...(prev ?? {}), ...(saved ?? {}) }));

      // bubble to parent list
      if (typeof onClientCreated === 'function') onClientCreated(saved);
    } catch (err) {
      console.error(err);
      showStatus(err.message || 'An error occurred while creating.', 'error');
    }
  };

  const onUpdateClick = async () => {
    try {
      const payload = buildPayload(viewRow);
      const saved = await handleUpdate(payload);
      console.log('Client updated successfully:', saved);
      showStatus('Client updated successfully', 'success');

      // keep local selection in sync
      setSelectedGroupRow(prev => ({ ...(prev ?? {}), ...(saved ?? {}) }));

      // bubble to parent list
      if (typeof onClientUpdated === 'function') onClientUpdated(saved);
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
      console.log('Client deleted successfully.');
      showStatus('Client deleted successfully', 'success');
      // (Optional) onClose?.();
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

  const actionLabel =
    mode === 'new' ? 'Create' :
    mode === 'edit' ? 'Update' :
    mode === 'delete' ? 'Delete' :
    'Save';

  const actionColor = mode === 'delete' ? 'error' : 'primary';

  const renderButtonRow = () => (
    <CRow className="mt-3" style={{ maxWidth: 900, margin: '0 auto' }}>
      <CCol style={{ display: 'flex', justifyContent: 'flex-start' }}>
        <Button variant="outlined" size="small" onClick={handleBack} disabled={tabIndex === 0}>
          Back
        </Button>
      </CCol>
      <CCol style={{ display: 'flex', justifyContent: 'flex-end', gap: '12px' }}>
        <Button
          variant="contained"
          size="small"
          color={actionColor}
          onClick={onPrimaryClick}
        >
          {actionLabel}
        </Button>
        <Button variant="outlined" size="small" onClick={handleNext} disabled={tabIndex === 3}>
          Next
        </Button>
      </CCol>
    </CRow>
  );

  const borderPanelStyle = {
    border: '1px solid #ccc',
    borderRadius: '4px',
    padding: '8px',
    height: '440px',
    maxWidth: '900px',
    width: '100%',
    margin: '0 auto',
  };

  const noBorderPanelStyle = {
    border: '0px solid transparent',
    borderRadius: '4px',
    padding: '8px',
    height: '440px',
    maxWidth: '900px',
    width: '100%',
    margin: '0 auto',
  };

  // ---- render --------------------------------------------------------------
  return (
    <>
      {/* Full-width header OUTSIDE the container */}
      <Box
        sx={{
          display: 'flex',
          justifyContent: 'space-between',
          alignItems: 'center',
          backgroundColor: '#1976d2',
          color: 'white',
          height: 40,
          mx: -2,
          mt: -2,
          px: 0,
          py: 0,
          borderTopLeftRadius: 8,
          borderTopRightRadius: 8,
        }}
      >
        <Box sx={{ fontWeight: 600, fontSize: '0.95rem', lineHeight: '40px', pl: 2 }}>
          Client Information
        </Box>
        <IconButton onClick={onClose} size="small" sx={{ color: 'white', mr: 1 }}>
          <CloseIcon fontSize="small" />
        </IconButton>
      </Box>

      {/* Main container (body) */}
      <Box sx={{ p: 2, height: 710, overflow: 'hidden', display: 'flex', flexDirection: 'column' }}>
        {/* Inputs under header (inline labels + fields on the same row) */}
        <Box sx={{ pb: 1, maxWidth: 900, mx: 'auto', width: '100%' }}>
          <CRow style={{ marginBottom: '12px', marginTop: 0 }}>
            {/* Client ID */}
            <CCol xs="6">
              <Box sx={{ display: 'flex', alignItems: 'center', gap: 1 }}>
                <Box component="label" sx={{ fontSize: '0.78rem', minWidth: 72 }}>
                  Client ID
                </Box>
                <TextField
                  label=""
                  value={viewRow.client ?? ''}
                  size="small"
                  disabled={!isEditable}
                  sx={{ ...sharedSx, minWidth: 160, flex: 1 }}
                  onChange={(e) =>
                    setSelectedGroupRow(prev => ({
                      ...(prev ?? makeEmptyClient()),
                      client: e.target.value,
                    }))
                  }
                />
              </Box>
            </CCol>

            {/* Name */}
            <CCol xs="6">
              <Box sx={{ display: 'flex', alignItems: 'center', gap: 1 }}>
                <Box component="label" sx={{ fontSize: '0.78rem', minWidth: 72 }}>
                  Name
                </Box>
                <TextField
                  label=""
                  value={viewRow.name ?? ''}
                  size="small"
                  disabled={!isEditable}
                  sx={{ ...sharedSx, flex: 1 }}
                  onChange={(e) =>
                    setSelectedGroupRow(prev => ({
                      ...(prev ?? makeEmptyClient()),
                      name: e.target.value,
                    }))
                  }
                />
              </Box>
            </CCol>
          </CRow>
        </Box>

        {/* Tabs */}
        <Tabs
          value={tabIndex}
          onChange={handleTabChange}
          variant="fullWidth"
          sx={{ mt: -0.5, mb: 2 }}
          TabIndicatorProps={{ sx: { width: '30px', left: 'calc(50% - 15px)' } }}
        >
          <Tab
            label={
              <Box sx={{ display: 'flex', alignItems: 'center', gap: 0.5 }}>
                <Box sx={{ width: 18, height: 18, borderRadius: '50%', backgroundColor: '#1976d2', color: 'white', fontSize: '0.7rem', display: 'flex', alignItems: 'center', justifyContent: 'center' }}>
                  1
                </Box>
                Client Information
              </Box>
            }
            sx={{ fontSize: '0.78rem', textTransform: 'none', minWidth: 205, maxWidth: 205, px: 1 }}
          />
          <Tab
            label={
              <Box sx={{ display: 'flex', alignItems: 'center', gap: 0.5 }}>
                <Box sx={{ width: 18, height: 18, borderRadius: '50%', backgroundColor: '#1976d2', color: 'white', fontSize: '0.7rem', display: 'flex', alignItems: 'center', justifyContent: 'center' }}>
                  2
                </Box>
                Client Email Setup
              </Box>
            }
            sx={{ fontSize: '0.78rem', textTransform: 'none', minWidth: 160, maxWidth: 170, px: 1 }}
          />
          <Tab
            label={
              <Box sx={{ display: 'flex', alignItems: 'center', gap: 0.5 }}>
                <Box sx={{ width: 18, height: 18, borderRadius: '50%', backgroundColor: '#1976d2', color: 'white', fontSize: '0.7rem', display: 'flex', alignItems: 'center', justifyContent: 'center' }}>
                  3
                </Box>
                Client Reports
              </Box>
            }
            sx={{ fontSize: '0.78rem', textTransform: 'none', minWidth: 135, maxWidth: 145, px: 1 }}
          />
          <Tab
            label={
              <Box sx={{ display: 'flex', alignItems: 'center', gap: 0.5 }}>
                <Box sx={{ width: 18, height: 18, borderRadius: '50%', backgroundColor: '#1976d2', color: 'white', fontSize: '0.7rem', display: 'flex', alignItems: 'center', justifyContent: 'center' }}>
                  4
                </Box>
                Client ATM/Cash Prefixes
              </Box>
            }
            sx={{ fontSize: '0.78rem', textTransform: 'none', minWidth: 195, maxWidth: 205, px: 1 }}
          />
        </Tabs>

        {/* Tab content */}
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
                    <EditClientEmailSetup selectedGroupRow={viewRow} isEditable={isEditable} />
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

      <Snackbar
        open={status.open}
        autoHideDuration={3000}
        onClose={handleStatusClose}
        anchorOrigin={{ vertical: 'top', horizontal: 'center' }}
      >
        <Alert
          onClose={handleStatusClose}
          severity={status.severity}
          variant="filled"
          sx={{ width: '100%' }}
        >
          {status.message}
        </Alert>
      </Snackbar>
    </>
  );
};

export default ClientInformationWindow;
