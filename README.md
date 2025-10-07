import React, { useState, useEffect } from 'react';
import { Box, IconButton, Tabs, Tab, Button } from '@mui/material';
import CloseIcon from '@mui/icons-material/Close';
import { CRow, CCol } from '@coreui/react';
import EditClientInformation from '../EditClientInformation';
import EditAtmCashPrefix from '../EditAtmCashPrefix';
import EditClientReport from '../EditClientReport';
import EditClientEmailSetup from '../EditClientEmailSetup';
import {
  TextField,
  FormControl,

} from '@mui/material';

const ClientInformationWindow = ({ onClose, selectedGroupRow, setSelectedGroupRow, mode }) => {
  const [tabIndex, setTabIndex] = useState(0);
  const [isEditable, setIsEditable] = useState(true);

  const makeEmptyClient = () => ({
    'client': '',
    'name': '',
    'addr': '',
    'city': '',
    'state': '',
    'zip': '',
    'contact': '',
    'phone': '',
    'active': true,
    'faxNumber': '',
    'billingSp': '',
    'reportBreakFlag': 0,
    'chLookUpType': 0,
    'excludeFromReport': false,
    'positiveReports': false,
    'subClientInd': false,
    'subClientXref': '',
    'amexIssued': false
});

  const handleSave = async () => {
    try {
      const response = await fetch('http://localhost:4444/api/client/save', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(selectedGroupRow),
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

  useEffect(() => {
    switch (mode) {
      case 'new':
        setIsEditable(true);
        setSelectedGroupRow(prev => (prev && Object.keys(prev).length ? prev : makeEmptyClient()));
        break;
      case 'edit':
        setIsEditable(true);
        break;
      case 'view':
      case 'delete':
      default:
        setIsEditable(false);
        break;
    }
  }, [mode]);

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

  const handleTabChange = (event, newValue) => {
    setTabIndex(newValue);
  };

  const handleNext = () => {
    setTabIndex((prev) => Math.min(prev + 1, 3));
  };

  const handleBack = () => {
    setTabIndex((prev) => Math.max(prev - 1, 0));
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
      <Box sx={{ display: 'flex', justifyContent: 'space-between', alignItems: 'center', mt: '-25px', backgroundColor: '#e9f2f8' }}>
        <div style={{ padding: '12px' }}>
          <CRow style={{ marginBottom: '12px', marginTop: '0px'}}>
            <CCol xs="6">
              <Box sx={{ display: 'flex', flexDirection: 'column', gap: '2px' }}>
                <label style={{ fontSize: '0.78rem', marginBottom: '2px' }}>Client ID</label>
                <TextField
                  label=""
                  value={selectedGroupRow.client || ''}
                  size="small"
                  disabled={!isEditable}
                  sx={{ ...sharedSx, minWidth: '160px' }}
                />
              </Box>
            </CCol>

            <CCol xs="6">
              <Box sx={{ display: 'flex', flexDirection: 'column', gap: '2px' }}>
                <label style={{ fontSize: '0.78rem', marginBottom: '2px' }}>Name</label>
                <TextField
                  label=""
                  value={selectedGroupRow.name || ''}
                  size="small"
                  fullWidth
                  disabled={!isEditable}
                  sx={sharedSx}
                />
              </Box>
            </CCol>
          </CRow>
        </div>
        <IconButton onClick={onClose} size="small">
          <CloseIcon fontSize="small" />
        </IconButton>
    </Box>

      <Tabs
          value={tabIndex}
          onChange={handleTabChange}
          variant="fullWidth"
          sx={{ mt: -0.5, mb: 2 }}
          TabIndicatorProps={{
            sx: {
              width: '30px',
              left: 'calc(50% - 15px)',
            },
          }}
        >
          <Tab
            label={
              <Box sx={{ display: 'flex', alignItems: 'center', gap: 0.5, flexShrink: 1, overflow: 'hidden', whiteSpace: 'nowrap' }}>
                <Box sx={{ width: 18, height: 18, borderRadius: '50%', backgroundColor: '#1976d2', color: 'white', fontSize: '0.7rem', display: 'flex', alignItems: 'center', justifyContent: 'center', flexShrink: 0 }}>
                  1
                </Box>
                Client Information
              </Box>
            }
            sx={{ fontSize: '0.78rem', textTransform: 'none', minWidth: 130, maxWidth: 140, paddingX: 1 }}
          />

          <Tab
            label={
              <Box sx={{ display: 'flex', alignItems: 'center', gap: 0.5, flexShrink: 1, overflow: 'hidden', whiteSpace: 'nowrap' }}>
                <Box sx={{ width: 18, height: 18, borderRadius: '50%', backgroundColor: '#1976d2', color: 'white', fontSize: '0.7rem', display: 'flex', alignItems: 'center', justifyContent: 'center', flexShrink: 0 }}>
                  2
                </Box>
                Client Email Setup
              </Box>
            }
            sx={{ fontSize: '0.78rem', textTransform: 'none', minWidth: 160, maxWidth: 170, paddingX: 1 }}
          />

          <Tab
            label={
              <Box sx={{ display: 'flex', alignItems: 'center', gap: 0.5, flexShrink: 1, overflow: 'hidden', whiteSpace: 'nowrap' }}>
                <Box sx={{ width: 18, height: 18, borderRadius: '50%', backgroundColor: '#1976d2', color: 'white', fontSize: '0.7rem', display: 'flex', alignItems: 'center', justifyContent: 'center', flexShrink: 0 }}>
                  3
                </Box>
                Client Reports
              </Box>
            }
            sx={{ fontSize: '0.78rem', textTransform: 'none', minWidth: 135, maxWidth: 145, paddingX: 1 }}
          />

          <Tab
            label={
              <Box sx={{ display: 'flex', alignItems: 'center', gap: 0.5, flexShrink: 1, overflow: 'hidden', whiteSpace: 'nowrap' }}>
                <Box sx={{ width: 18, height: 18, borderRadius: '50%', backgroundColor: '#1976d2', color: 'white', fontSize: '0.7rem', display: 'flex', alignItems: 'center', justifyContent: 'center', flexShrink: 0 }}>
                  4
                </Box>
                Client ATM/Cash Prefixes
              </Box>
            }
            sx={{ fontSize: '0.78rem', textTransform: 'none', minWidth: 195, maxWidth: 205, paddingX: 1 }}
          />
        </Tabs>

      <Box>
        {tabIndex === 0 && (
          <>
            <CRow className="mb-2" style={{ border: '1px solid #ccc', borderRadius: '4px', padding: '8px', height: '470px' }}>
              <CCol>
                <div style={{ fontSize: '0.78rem', paddingTop: '12px', height: '100%' }}>
                  <EditClientInformation
                    selectedGroupRow={selectedGroupRow}
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
                  <EditClientEmailSetup selectedGroupRow={selectedGroupRow} isEditable={isEditable} />
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
                  <EditClientReport selectedGroupRow={selectedGroupRow} isEditable={isEditable} />
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
                  <EditAtmCashPrefix selectedGroupRow={selectedGroupRow} isEditable={isEditable} />
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
