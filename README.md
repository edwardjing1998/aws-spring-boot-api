// ClientInformationWindow.jsx
import React, { useState, useEffect } from 'react';
import { Box, IconButton, Tabs, Tab, Button, TextField } from '@mui/material';
import CloseIcon from '@mui/icons-material/Close';
import { CRow, CCol } from '@coreui/react';

import EditClientInformation from '../EditClientInformation';
import EditAtmCashPrefix from '../EditAtmCashPrefix';
import EditClientReport from '../EditClientReport';
import EditClientEmailSetup from '../EditClientEmailSetup';

const ClientInformationWindow = ({
  onClose,
  selectedGroupRow,
  setSelectedGroupRow,
  mode,
}) => {
  const [tabIndex, setTabIndex] = useState(0);
  const [isEditable, setIsEditable] = useState(true);

  /** Create a blank client only for "new" mode */
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

  /** Seed state for new/edit/view/delete */
  useEffect(() => {
    if (mode === 'new') {
      setIsEditable(true);
      // seed only if parent didn't pass anything
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

  /** Safe view model so render never touches undefined */
  const viewRow = mode === 'new'
    ? (selectedGroupRow ?? makeEmptyClient())
    : (selectedGroupRow ?? {});

  const sharedSx = {
    '& .MuiInputBase-root': {
      height: '30px',
      fontSize: '0.78rem',
    },
    '& .MuiInputBase-input': {
      padding: '4px 4px',
      height: '30px',
      fontSize: '0.78rem',
      lineHeight: '1rem',
    },
    '& .MuiInputLabel-root': {
      fontSize: '0.78rem',
      lineHeight: '1rem',
    },
    '& .MuiInputBase-input.Mui-disabled': {
      color: 'black',
      WebkitTextFillColor: 'black',
    },
    '& .MuiInputLabel-root.Mui-disabled': {
      color: 'black',
    },
  };

  const handleTabChange = (_e, newValue) => setTabIndex(newValue);
  const handleNext = () => setTabIndex(prev => Math.min(prev + 1, 3));
  const handleBack = () => setTabIndex(prev => Math.max(prev - 1, 0));

  const handleSave = async () => {
    try {
      const response = await fetch('http://localhost:4444/api/client/save', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        // use viewRow to ensure we always send a full object
        body: JSON.stringify(viewRow),
      });
      if (!response.ok) {
        const text = await response.text();
        throw new Error(`Save failed: ${response.status} ${text}`);
      }
      const saved = await response.json();
      console.log('Client saved successfully:', saved);
    } catch (err) {
      console.error(err);
      alert('An error occurred while saving. Check console for details.');
    }
  };

  const renderButtonRow = () => (
    <CRow className="mt-3">
      <CCol style={{ display: 'flex', justifyContent: 'flex-start' }}>
        <Button variant="outlined" size="small" onClick={handleBack} disabled={tabIndex === 0}>
          Back
        </Button>
      </CCol>
      <CCol style={{ display: 'flex', justifyContent: 'flex-end', gap: '12px' }}>
        <Button variant="contained" size="small" onClick={handleSave}>
          Save
        </Button>
        <Button variant="outlined" size="small" onClick={handleNext} disabled={tabIndex === 3}>
          Next
        </Button>
      </CCol>
    </CRow>
  );

  return (
    <Box sx={{ padding: '16px', overflow: 'visible', height: '750px' }}>
      {/* Header */}
      <Box
        sx={{
          display: 'flex',
          justifyContent: 'space-between',
          alignItems: 'center',
          mt: '-25px',
          backgroundColor: '#e9f2f8',
        }}
      >
        <div style={{ padding: '12px' }}>
          <CRow style={{ marginBottom: '12px', marginTop: '0px' }}>
            <CCol xs="6">
              <Box sx={{ display: 'flex', flexDirection: 'column', gap: '2px' }}>
                <label style={{ fontSize: '0.78rem', marginBottom: '2px' }}>Client ID</label>
                <TextField
                  label=""
                  value={viewRow.client ?? ''}
                  size="small"
                  disabled={!isEditable}
                  sx={{ ...sharedSx, minWidth: '160px' }}
                  onChange={(e) =>
                    setSelectedGroupRow(prev => ({
                      ...(prev ?? makeEmptyClient()),
                      client: e.target.value,
                    }))
                  }
                />
              </Box>
            </CCol>

            <CCol xs="6">
              <Box sx={{ display: 'flex', flexDirection: 'column', gap: '2px' }}>
                <label style={{ fontSize: '0.78rem', marginBottom: '2px' }}>Name</label>
                <TextField
                  label=""
                  value={viewRow.name ?? ''}
                  size="small"
                  fullWidth
                  disabled={!isEditable}
                  sx={sharedSx}
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
        </div>

        <IconButton onClick={onClose} size="small">
          <CloseIcon fontSize="small" />
        </IconButton>
      </Box>

      {/* Tabs */}
      <Tabs
        value={tabIndex}
        onChange={handleTabChange}
        variant="fullWidth"
        sx={{ mt: -0.5, mb: 2 }}
        TabIndicatorProps={{
          sx: { width: '30px', left: 'calc(50% - 15px)' },
        }}
      >
        <Tab
          label={
            <Box sx={{ display: 'flex', alignItems: 'center', gap: 0.5, overflow: 'hidden', whiteSpace: 'nowrap' }}>
              <Box sx={{ width: 18, height: 18, borderRadius: '50%', backgroundColor: '#1976d2', color: 'white', fontSize: '0.7rem', display: 'flex', alignItems: 'center', justifyContent: 'center' }}>
                1
              </Box>
              Client Information
            </Box>
          }
          sx={{ fontSize: '0.78rem', textTransform: 'none', minWidth: 130, maxWidth: 140, px: 1 }}
        />
        <Tab
          label={
            <Box sx={{ display: 'flex', alignItems: 'center', gap: 0.5, overflow: 'hidden', whiteSpace: 'nowrap' }}>
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
            <Box sx={{ display: 'flex', alignItems: 'center', gap: 0.5, overflow: 'hidden', whiteSpace: 'nowrap' }}>
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
            <Box sx={{ display: 'flex', alignItems: 'center', gap: 0.5, overflow: 'hidden', whiteSpace: 'nowrap' }}>
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
      <Box>
        {tabIndex === 0 && (
          <>
            <CRow className="mb-2" style={{ border: '1px solid #ccc', borderRadius: '4px', padding: '8px', height: '470px' }}>
              <CCol>
                <div style={{ fontSize: '0.78rem', paddingTop: '12px', height: '100%' }}>
                  <EditClientInformation
                    selectedGroupRow={viewRow}
                    isEditable={isEditable}
                    setSelectedGroupRow={setSelectedGroupRow}
                  />
                </div>
              </CCol>
            </CRow>
            {renderButtonRow()}
          </>
        )}

        {tabIndex === 1 && (
          <>
            <CRow className="mb-2" style={{ border: '1px solid #ccc', borderRadius: '4px', padding: '8px', height: '470px' }}>
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
            <CRow className="mb-2" style={{ border: '1px solid #ccc', borderRadius: '4px', padding: '8px', height: '470px' }}>
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
            <CRow className="mb-2" style={{ border: '1px solid #ccc', borderRadius: '4px', padding: '8px', height: '470px' }}>
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
  );
};

export default ClientInformationWindow;
