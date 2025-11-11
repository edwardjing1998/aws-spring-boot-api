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
  mode,
  onClientUpdated,
}) => {
  const [updating, setUpdating] = useState(false);

  const MAX = { client: 4, name: 30, addr: 35, city: 18, zip: 9, contact: 20, billingSp: 8 };
  const addrLen = (selectedGroupRow?.addr ?? "").length;
  const cityLen = (selectedGroupRow?.city ?? '').length;
  const zipLen = (selectedGroupRow?.zip ?? '').length;
  const contactLen = (selectedGroupRow?.contact ?? '').length;
  const billingLen = (selectedGroupRow?.billingSp ?? '').length;

  const DIAL_CODES = [
    { value: '+1', label: 'US/CA' },
    { value: '+44', label: 'UK' },
    { value: '+61', label: 'AU' },
    { value: '+91', label: 'IN' },
  ];

  const US_STATES = [
    { code: 'AL', name: 'Alabama' }, { code: 'AK', name: 'Alaska' },
    { code: 'AZ', name: 'Arizona' }, { code: 'AR', name: 'Arkansas' },
    { code: 'CA', name: 'California' }, { code: 'CO', name: 'Colorado' },
    { code: 'CT', name: 'Connecticut' }, { code: 'DE', name: 'Delaware' },
    { code: 'FL', name: 'Florida' }, { code: 'GA', name: 'Georgia' },
    { code: 'HI', name: 'Hawaii' }, { code: 'ID', name: 'Idaho' },
    { code: 'IL', name: 'Illinois' }, { code: 'IN', name: 'Indiana' },
    { code: 'IA', name: 'Iowa' }, { code: 'KS', name: 'Kansas' },
    { code: 'KY', name: 'Kentucky' }, { code: 'LA', name: 'Louisiana' },
    { code: 'ME', name: 'Maine' }, { code: 'MD', name: 'Maryland' },
    { code: 'MA', name: 'Massachusetts' }, { code: 'MI', name: 'Michigan' },
    { code: 'MN', name: 'Minnesota' }, { code: 'MS', name: 'Mississippi' },
    { code: 'MO', name: 'Missouri' }, { code: 'MT', name: 'Montana' },
    { code: 'NE', name: 'Nebraska' }, { code: 'NV', name: 'Nevada' },
    { code: 'NH', name: 'New Hampshire' }, { code: 'NJ', name: 'New Jersey' },
    { code: 'NM', name: 'New Mexico' }, { code: 'NY', name: 'New York' },
    { code: 'NC', name: 'North Carolina' }, { code: 'ND', name: 'North Dakota' },
    { code: 'OH', name: 'Ohio' }, { code: 'OK', name: 'Oklahoma' },
    { code: 'OR', name: 'Oregon' }, { code: 'PA', name: 'Pennsylvania' },
    { code: 'RI', name: 'Rhode Island' }, { code: 'SC', name: 'South Carolina' },
    { code: 'SD', name: 'South Dakota' }, { code: 'TN', name: 'Tennessee' },
    { code: 'TX', name: 'Texas' }, { code: 'UT', name: 'Utah' },
    { code: 'VT', name: 'Vermont' }, { code: 'VA', name: 'Virginia' },
    { code: 'WA', name: 'Washington' }, { code: 'WV', name: 'West Virginia' },
    { code: 'WI', name: 'Wisconsin' }, { code: 'WY', name: 'Wyoming' },
  ];

  const handleChange = (field) => (e) => {
    setSelectedGroupRow((prev) => ({ ...prev, [field]: e.target.value }));
  };

  const handleCheckboxChange = (field) => (e) => {
    setSelectedGroupRow((prev) => ({ ...prev, [field]: e.target.checked }));
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
      {/* Address Line / City / State / Zip */}
      <CRow style={{ marginBottom: '20px' }}>
        <CCol xs="6">
          <FormControl fullWidth>
            <label style={{ fontSize: '0.78rem', marginBottom: '4px' }}>Address Line</label>
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

        <CCol xs="2">
          <FormControl fullWidth>
            <label style={{ fontSize: '0.78rem', marginBottom: '4px' }}>State</label>
            <TextField
              select
              value={selectedGroupRow.state || ''}
              onChange={handleChange('state')}
              size="small"
              fullWidth
              disabled={!isEditable}
              sx={sharedSx}
              SelectProps={{
                MenuProps: {
                  sx: {
                    '& .MuiMenuItem-root': { fontSize: '0.72rem', minHeight: 28, lineHeight: 1.2 },
                  },
                  PaperProps: { style: { maxHeight: 320 } },
                },
              }}
            >
              {US_STATES.map((s) => (
                <MenuItem key={s.code} value={s.code}>
                  {s.name} ({s.code})
                </MenuItem>
              ))}
            </TextField>
          </FormControl>
        </CCol>

        <CCol xs="2">
          <FormControl fullWidth>
            <label style={{ fontSize: '0.78rem', marginBottom: '4px' }}>Zip Code</label>
            <TextField
              label=""
              value={selectedGroupRow.zip || ''}
              onChange={handleChange('zip')}
              size="small"
              fullWidth
              disabled={!isEditable}
              sx={sharedSx}
              inputProps={{ maxLength: MAX.zip, inputMode: 'numeric' }}
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
              {/* Country Code — like State select */}
              <TextField
                select
                value={selectedGroupRow.phoneCountryCode || '+1'}
                onChange={handleChange('phoneCountryCode')}
                size="small"
                disabled={!isEditable}
                sx={{
                  ...sharedSx,
                  width: 90,
                  '& .MuiInputBase-root': { height: '24px' },
                  '& .MuiSelect-select': {
                    minHeight: 0,
                    paddingTop: 0,
                    paddingBottom: 0,
                    lineHeight: '24px',
                    fontSize: '0.78rem',
                    display: 'flex',
                    alignItems: 'center',
                  },
                }}
                SelectProps={{
                  displayEmpty: true,
                  renderValue: (val) =>
                    (DIAL_CODES.find(o => o.value === val)?.label || String(val || '')),
                  MenuProps: {
                    sx: {
                      '& .MuiMenuItem-root': {
                        fontSize: '0.72rem',
                        minHeight: 26,
                        lineHeight: 1.2,
                      },
                    },
                    PaperProps: { style: { maxHeight: 260 } },
                  },
                }}
              >
                {DIAL_CODES.map(opt => (
                  <MenuItem key={opt.value} value={opt.value}>
                    {opt.label}
                  </MenuItem>
                ))}
              </TextField>

              {/* Phone number */}
              <Box
                sx={{
                  display: 'flex',
                  gap: 1,
                  alignItems: 'flex-start',
                  flex: 1,
                  maxWidth: 'calc(100% - 98px)',
                }}
              >
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
            <TextField
              label=""
              value={selectedGroupRow.faxNumber || ''}
              onChange={handleChange('faxNumber')}
              size="small"
              fullWidth
              disabled={!isEditable}
              sx={sharedSx}
            />
          </FormControl>
        </CCol>
      </CRow>
    </div>
  );
};

export default EditClientInformation;
