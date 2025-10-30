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
  mode, // 👈 receive mode from parent
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
      const saved = await response.json();
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
      const saved = await response.json();
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


