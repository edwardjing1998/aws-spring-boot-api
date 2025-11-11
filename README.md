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
import ReactCountryFlag from "react-country-flag";


  const EditClientInformation = ({
    selectedGroupRow,
    isEditable,
    setSelectedGroupRow,
    mode,              // from parent
    onClientUpdated,   // ✅ new: bubble up to parent
  }) => {

  const [updating, setUpdating] = useState(false);

  const MAX = {client: 4, name: 30, addr: 35, city: 18, zip: 9, contact: 20, billingSp: 8 };
  const addrLen = (selectedGroupRow?.addr ?? "").length;
  const cityLen = (selectedGroupRow?.city ?? '').length;

  const zipLen      = (selectedGroupRow?.zip ?? '').length;
  const contactLen  = (selectedGroupRow?.contact ?? '').length;
  const billingLen  = (selectedGroupRow?.billingSp ?? '').length;

  const CCODES = { '+1':'US', '+44':'UK', '+61':'AU', '+91':'IN', /* add more as needed */ };

  const COUNTRIES = [
  { value: 'US', label: 'US', dial: '+1',  flag: 'US' },
  { value: 'CA', label: 'CA', dial: '+1',  flag: 'CA' },
  { value: 'GB', label: 'UK', dial: '+44', flag: 'GB' },
  { value: 'AU', label: 'AU', dial: '+61', flag: 'AU' },
  { value: 'IN', label: 'IN', dial: '+91', flag: 'IN' },
];

const getCountry = (val) => COUNTRIES.find(o => o.value === val) || COUNTRIES[0];

const DIAL_CODES = [
  { value: '+1',  label: 'US/CA' }, 
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
            <label
              htmlFor="addr-input"
              style={{
                fontSize: '0.78rem',
                marginBottom: '4px',
                display: 'flex',
                alignItems: 'center',
                gap: 6,
              }}
            >
              Address Line
              <span
                id="addr-counter"
                style={{ fontSize: '0.72rem', color: addrLen >= MAX.addr ? '#d32f2f' : 'gray' }}
              >
                ({addrLen}/{MAX.addr})
              </span>
            </label>

            <TextField
              id="addr-input"
              label=""
              value={selectedGroupRow.addr || ''}
              onChange={handleChange('addr')}
              size="small"
              fullWidth
              disabled={!isEditable}
              sx={sharedSx}
              inputProps={{ maxLength: MAX.addr, 'aria-describedby': 'addr-counter' }}
            />
          </FormControl>
        </CCol>

        {/* City */}
        <CCol xs="2">
          <FormControl fullWidth>
            <label
              htmlFor="city-input"
              style={{
                fontSize: '0.78rem',
                marginBottom: '4px',
                display: 'flex',
                alignItems: 'center',
                gap: 6,
              }}
            >
              City
              <span
                id="city-counter"
                style={{
                  fontSize: '0.72rem',
                  color: cityLen >= MAX.city ? '#d32f2f' : 'gray',
                }}
              >
                ({cityLen}/{MAX.city})
              </span>
            </label>

            <TextField
              id="city-input"
              label=""
              value={selectedGroupRow.city || ''}
              onChange={handleChange('city')}
              size="small"
              fullWidth
              disabled={!isEditable}
              sx={sharedSx}
              inputProps={{ maxLength: MAX.city, 'aria-describedby': 'city-counter' }}
            />
          </FormControl>
        </CCol>

        {/* State */}
        <CCol xs="2">
          <FormControl fullWidth>
            <label style={{ fontSize: '0.78rem', marginBottom: 4 }}>State</label>
            <TextField
                select
                label=""
                value={selectedGroupRow.state || ''}
                onChange={(e) =>
                  setSelectedGroupRow(prev => ({ ...(prev ?? {}), state: e.target.value }))
                }
                size="small"
                fullWidth
                disabled={!isEditable}
                sx={sharedSx}
                SelectProps={{
                  MenuProps: {
                    // ↓ smaller font just for the menu items
                    sx: {
                      '& .MuiMenuItem-root': {
                        fontSize: '0.72rem',  // tweak to taste
                        minHeight: 30,
                        lineHeight: 1.2,
                      },
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

        {/* Zip */}
        <CCol xs="2">
          <FormControl fullWidth>
            <label
              htmlFor="zip-input"
              style={{ fontSize: '0.78rem', marginBottom: '4px', display: 'flex', alignItems: 'center', gap: 6 }}
            >
              Zip Code
              <span
                id="zip-counter"
                style={{ fontSize: '0.72rem', color: zipLen >= MAX.zip ? '#d32f2f' : 'gray' }}
              >
                ({zipLen}/{MAX.zip})
              </span>
            </label>

            <TextField
              id="zip-input"
              value={selectedGroupRow.zip || ''}
              onChange={handleChange('zip')}
              size="small"
              fullWidth
              disabled={!isEditable}
              sx={sharedSx}
              variant="outlined"
              label=""
              inputProps={{
                maxLength: MAX.zip,
                'aria-describedby': 'zip-counter',
                inputMode: 'numeric', // optional UX hint on mobile keyboards
              }}
            />
          </FormControl>
        </CCol>
      </CRow>

      {/* Contact / Phone / Fax */}
      <CRow style={{ marginBottom: '20px' }}>
        <CCol xs="4">
          <FormControl fullWidth>
            <label
              htmlFor="contact-input"
              style={{ fontSize: '0.78rem', marginBottom: '4px', display: 'flex', alignItems: 'center', gap: 6 }}
            >
              Contact
              <span
                id="contact-counter"
                style={{ fontSize: '0.72rem', color: contactLen >= MAX.contact ? '#d32f2f' : 'gray' }}
              >
                ({contactLen}/{MAX.contact})
              </span>
            </label>

            <TextField
              id="contact-input"
              label=""
              value={selectedGroupRow.contact || ''}
              onChange={handleChange('contact')}
              size="small"
              fullWidth
              disabled={!isEditable}
              sx={sharedSx}
              inputProps={{
                maxLength: MAX.contact,
                'aria-describedby': 'contact-counter',
              }}
            />
          </FormControl>
        </CCol>

        <CCol xs="4">
          <FormControl fullWidth>
            <label style={{ fontSize: '0.78rem', marginBottom: '4px' }}>Phone</label>
            <Box sx={{ display: 'flex', gap: 1 }}>
              {/* Country Code */}
              <FormControl sx={{ minWidth: 90 }} size="small">
  <Select
    value={selectedGroupRow.phoneCountryCode || '+1'}
    onChange={handleChange('phoneCountryCode')}
    disabled={!isEditable}
    displayEmpty
    IconComponent={() => null} 
    renderValue={(val) => (DIAL_CODES.find(o => o.value === val)?.label || String(val))}
    sx={{
      ...sharedSx,
      width: 70,

      '& .MuiOutlinedInput-root': {
        height: 24,
        minHeight: 24,
      },

      '& .MuiOutlinedInput-input': {
        paddingTop: 0,
        paddingBottom: 0,
        paddingLeft: 8,
        paddingRight: 8,      
        fontSize: '0.78rem',
        height: 24,
        lineHeight: '24px',
        display: 'flex',
        alignItems: 'center',
      },
      '& .MuiSelect-select': {
        paddingTop: 0,
        paddingBottom: 0,
        minHeight: 0,
        fontSize: '0.78rem',
        height: 24,
        lineHeight: '24px',
        display: 'flex',
        alignItems: 'center',
      },
      '& .MuiSelect-icon, & .MuiSelect-iconOutlined': { display: 'none' },
    }}
    MenuProps={{
      sx: {
        '& .MuiMenuItem-root': { fontSize: '0.72rem', minHeight: 26, lineHeight: 1.2 },
      },
      PaperProps: { style: { maxHeight: 260 } },
    }}
  >
    {DIAL_CODES.map(opt => (
      <MenuItem key={opt.value} value={opt.value}>
        {opt.label}
      </MenuItem>
    ))}
  </Select>
</FormControl>

              {/* Number */}
                  <Box sx={{
                      display: 'flex',
                      gap: 1,
                      alignItems: 'flex-start',
                      flex: 1,
                      maxWidth: 'calc(100% - 98px)', // 90 (select) + 8 (gap=1)
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
                 MenuProps={{
                    sx: {
                      '& .MuiMenuItem-root': {
                        fontSize: '0.72rem',     // ↓ menu item text smaller
                        minHeight: 28,
                        lineHeight: 1.2,
                      },
                    },
                    PaperProps: { style: { maxHeight: 280 } },
                  }}
                >
                    <MenuItem value="+1">
                      <ReactCountryFlag countryCode="US" svg style={{ width: '1em', height: '1em', marginRight: 6 }} />
                      US
                    </MenuItem>
                    <MenuItem value="+1">
                      <ReactCountryFlag countryCode="CA" svg style={{ width: '1em', height: '1em', marginRight: 6 }} />
                      CA
                    </MenuItem>
                    <MenuItem value="+44">
                      <ReactCountryFlag countryCode="GB" svg style={{ width: '1em', height: '1em', marginRight: 6 }} />
                      UK
                    </MenuItem>
                    <MenuItem value="+61">
                      <ReactCountryFlag countryCode="AU" svg style={{ width: '1em', height: '1em', marginRight: 6 }} />
                      AU
                    </MenuItem>
                    <MenuItem value="+91">
                      <ReactCountryFlag countryCode="IN" svg style={{ width: '1em', height: '1em', marginRight: 6 }} />
                      IN
                    </MenuItem>
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
            <label
              htmlFor="billingsp-input"
              style={{ fontSize: '0.78rem', marginBottom: '4px', display: 'flex', alignItems: 'center', gap: 6 }}
            >
              Billing Sys/Prin
              <span
                id="billingsp-counter"
                style={{ fontSize: '0.72rem', color: billingLen >= MAX.billingSp ? '#d32f2f' : 'gray' }}
              >
                ({billingLen}/{MAX.billingSp})
              </span>
            </label>

            <TextField
              id="billingsp-input"
              label=""
              value={selectedGroupRow.billingSp || ''}
              onChange={handleChange('billingSp')}
              size="small"
              fullWidth
              disabled={!isEditable}
              sx={sharedSx}
              inputProps={{
                maxLength: MAX.billingSp,
                'aria-describedby': 'billingsp-counter',
                inputMode: 'numeric', // optional if it’s digits only
              }}
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
