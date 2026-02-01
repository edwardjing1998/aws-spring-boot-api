import React from 'react';
import {
  Box,
  TextField,
  FormControl,
  Select,
  MenuItem,
  FormControlLabel,
  Checkbox,
  SelectChangeEvent,
} from '@mui/material';

import { CRow, CCol } from '@coreui/react';
import { CCard, CCardBody } from '@coreui/react';

import ReactCountryFlag from 'react-country-flag';
import { REPORT_BREAK_OPTIONS, SEARCH_TYPE_OPTIONS } from '../utils/ClientInfoFieldValueMapping';

import { ClientGroupRow, EditClientInformationProps } from './Client.type';

const EditClientInformation: React.FC<EditClientInformationProps> = ({
  selectedGroupRow,
  isEditable,
  setSelectedGroupRow,
  mode,              // from parent
  onClientUpdated,   // ✅ bubble up to parent
}) => {
  const formatPhone = (value: string) => {
    const digits = value.replace(/\D/g, '');

    const part1 = digits.slice(0, 3);
    const part2 = digits.slice(3, 6);
    const part3 = digits.slice(6, 13);

    if (digits.length <= 3) return part1;
    if (digits.length <= 6) return `${part1}-${part2}`;
    if (digits.length <= 13) return `${part1}-${part2}-${part3}`;

    return `${part1}-${part2}-${part3}`;
  };

  const MAX = {
    client: 4,
    name: 30,
    addr: 35,
    city: 18,
    zip: 9,
    contact: 20,
    billingSp: 8,
    subClientXref: 4,
    phone: 13,
    faxNumber: 13,
  };

  const addrLen = (selectedGroupRow?.addr ?? '').length;
  const cityLen = (selectedGroupRow?.city ?? '').length;
  const zipLen = (selectedGroupRow?.zip ?? '').length;
  const contactLen = (selectedGroupRow?.contact ?? '').length;
  const billingLen = (selectedGroupRow?.billingSp ?? '').length;

  // ✅ IMPORTANT: tolerate different field casing + nulls
  const xrefValue =
    (selectedGroupRow as any)?.subClientXref ??
    (selectedGroupRow as any)?.subClientXRef ??
    '';

  const subClientXrefLen = String(xrefValue ?? '').length;

  const faxNumberLen = (selectedGroupRow?.faxNumber ?? '').length;
  const phoneLen = (selectedGroupRow?.phone ?? '').length;

  const DIAL_CODES = [
    { value: '1', label: 'US/CA', countryCode: 'US' },
    { value: '44', label: 'UK', countryCode: 'GB' },
    { value: '61', label: 'AU', countryCode: 'AU' },
    { value: '91', label: 'IN', countryCode: 'IN' },
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

  const handleChange = (field: keyof ClientGroupRow) => (
    e: React.ChangeEvent<HTMLInputElement | HTMLTextAreaElement> | SelectChangeEvent<string>,
  ) => {
    setSelectedGroupRow((prev) => (prev ? { ...prev, [field]: e.target.value } : null));
  };

  const handleCheckboxChange = (field: keyof ClientGroupRow) => (
    e: React.ChangeEvent<HTMLInputElement>,
  ) => {
    setSelectedGroupRow((prev) => (prev ? { ...prev, [field]: e.target.checked } : null));
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
    '& .MuiInputBase-input.Mui-disabled': {
      color: 'black',
      WebkitTextFillColor: 'black',
    },
    '& .MuiInputLabel-root.Mui-disabled': { color: 'black' },
  };

  return (
    <div style={{ padding: '12px' }}>
      {/* Address / City / State / Zip */}
      <CCard
        style={{
          height: '75px',
          marginBottom: '4px',
          marginTop: '2px',
          border: 'none',
          borderBottom: '1px solid #ccc',
          boxShadow: 'none',
          borderRadius: '0px',
        }}
      >
        <CCardBody
          className="d-flex align-items-center"
          style={{
            padding: '0.25rem 0.5rem',
            height: '100%',
            backgroundColor: 'transparent',
          }}
        >
          <CRow style={{ marginBottom: '20px' }}>
            {/* Address Line */}
            <CCol xs={5}>
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
                    style={{
                      fontSize: '0.72rem',
                      color: addrLen >= MAX.addr ? '#d32f2f' : 'gray',
                    }}
                  >
                    ({addrLen}/{MAX.addr})
                  </span>
                </label>

                <TextField
                  id="addr-input"
                  label=""
                  value={selectedGroupRow?.addr || ''}
                  onChange={handleChange('addr')}
                  size="small"
                  fullWidth
                  disabled={!isEditable}
                  sx={sharedSx}
                  inputProps={{
                    maxLength: MAX.addr,
                    'aria-describedby': 'addr-counter',
                  }}
                />
              </FormControl>
            </CCol>

            {/* City */}
            <CCol xs={2}>
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
                  value={selectedGroupRow?.city || ''}
                  onChange={handleChange('city')}
                  size="small"
                  fullWidth
                  disabled={!isEditable}
                  sx={sharedSx}
                  inputProps={{
                    maxLength: MAX.city,
                    'aria-describedby': 'city-counter',
                  }}
                />
              </FormControl>
            </CCol>

            {/* State */}
            <CCol xs={3}>
              <FormControl fullWidth>
                <label style={{ fontSize: '0.78rem', marginBottom: 4 }}>State</label>
                <TextField
                  select
                  label=""
                  value={selectedGroupRow?.state || ''}
                  onChange={handleChange('state')}
                  size="small"
                  fullWidth
                  disabled={!isEditable}
                  sx={sharedSx}
                  SelectProps={{
                    MenuProps: {
                      sx: {
                        '& .MuiMenuItem-root': {
                          fontSize: '0.72rem',
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
            <CCol xs={2}>
              <FormControl fullWidth>
                <label
                  htmlFor="zip-input"
                  style={{
                    fontSize: '0.78rem',
                    marginBottom: '4px',
                    display: 'flex',
                    alignItems: 'center',
                    gap: 6,
                  }}
                >
                  Zip
                  <span
                    id="zip-counter"
                    style={{
                      fontSize: '0.78rem',
                      color: zipLen >= MAX.zip ? '#d32f2f' : 'gray',
                    }}
                  >
                    ({zipLen}/{MAX.zip})
                  </span>
                </label>

                <TextField
                  id="zip-input"
                  value={selectedGroupRow?.zip || ''}
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
                    inputMode: 'numeric',
                  }}
                />
              </FormControl>
            </CCol>
          </CRow>
        </CCardBody>
      </CCard>

      {/* Contact / Phone / Fax */}
      <CCard
        style={{
          height: '95px',
          marginBottom: '4px',
          marginTop: '2px',
          border: 'none',
          borderBottom: '1px solid #ccc',
          boxShadow: 'none',
          borderRadius: '0px',
        }}
      >
        <CCardBody
          className="d-flex align-items-center"
          style={{
            padding: '0.25rem 0.5rem',
            height: '100%',
            backgroundColor: 'transparent',
          }}
        >
          <CRow style={{ marginBottom: '60px', marginTop: '60px' }}>
            {/* Contact */}
            <CCol xs={4}>
              <FormControl fullWidth>
                <label
                  htmlFor="contact-input"
                  style={{
                    fontSize: '0.78rem',
                    marginBottom: '4px',
                    display: 'flex',
                    alignItems: 'center',
                    gap: 6,
                  }}
                >
                  Contact
                  <span
                    id="contact-counter"
                    style={{
                      fontSize: '0.72rem',
                      color: contactLen >= MAX.contact ? '#d32f2f' : 'gray',
                    }}
                  >
                    ({contactLen}/{MAX.contact})
                  </span>
                </label>

                <TextField
                  id="contact-input"
                  label=""
                  value={selectedGroupRow?.contact || ''}
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

            {/* Phone */}
            <CCol xs={4}>
              <FormControl fullWidth>
                <label style={{ fontSize: '0.78rem', marginBottom: '4px' }}>
                  Phone
                  <span
                    id="phone-counter"
                    style={{
                      fontSize: '0.72rem',
                      color: phoneLen >= MAX.phone ? '#d32f2f' : 'gray',
                      marginLeft: 6,
                    }}
                  >
                    ({phoneLen}/{MAX.phone})
                  </span>
                </label>

                <Box sx={{ display: 'flex', gap: 1 }}>
                  <TextField
                    select
                    value={selectedGroupRow?.phoneCountryCode || '1'}
                    onChange={handleChange('phoneCountryCode')}
                    size="small"
                    disabled={!isEditable}
                    sx={{
                      ...sharedSx,
                      width: 110,
                      '& .MuiOutlinedInput-root': { height: 30, minHeight: 30 },
                      '& .MuiInputBase-root': { height: 30 },
                      '& .MuiSelect-select': {
                        minHeight: 0,
                        paddingTop: 4,
                        paddingBottom: 4,
                        lineHeight: '30px',
                        fontSize: '0.78rem',
                        display: 'flex',
                        alignItems: 'center',
                      },
                    }}
                    SelectProps={{
                      displayEmpty: true,
                      renderValue: (val) => {
                        const valStr = val as string;
                        const opt =
                          DIAL_CODES.find((o) => o.value === valStr) ||
                          { value: valStr || '', label: valStr || '', countryCode: 'US' };

                        return (
                          <Box sx={{ display: 'flex', alignItems: 'center', gap: 0.5 }}>
                            {!!opt.countryCode && (
                              <ReactCountryFlag
                                countryCode={opt.countryCode}
                                svg
                                style={{ width: '1.1em', height: '1.1em' }}
                              />
                            )}
                            <span>{opt.value}</span>
                          </Box>
                        );
                      },
                      MenuProps: {
                        sx: {
                          '& .MuiMenuItem-root': {
                            fontSize: '0.72rem',
                            minHeight: 30,
                            lineHeight: 1.2,
                          },
                        },
                        PaperProps: { style: { maxHeight: 280 } },
                      },
                    }}
                  >
                    {DIAL_CODES.map((opt) => (
                      <MenuItem key={opt.value} value={opt.value}>
                        <Box sx={{ display: 'flex', alignItems: 'center', gap: 0.75 }}>
                          <ReactCountryFlag
                            countryCode={opt.countryCode}
                            svg
                            style={{ width: '1.1em', height: '1.1em' }}
                          />
                          <span>
                            {opt.label} ({opt.value})
                          </span>
                        </Box>
                      </MenuItem>
                    ))}
                  </TextField>

                  <TextField
                    label=""
                    value={selectedGroupRow?.phone || ''}
                    onChange={(e) => {
                      const formatted = formatPhone(e.target.value);
                      setSelectedGroupRow((prev) =>
                        prev ? { ...prev, phone: formatted } : null,
                      );
                    }}
                    size="small"
                    fullWidth
                    disabled={!isEditable}
                    sx={sharedSx}
                    inputProps={{ maxLength: 13 }}
                  />
                </Box>
              </FormControl>
            </CCol>

            {/* Fax */}
            <CCol xs={4}>
              <FormControl fullWidth>
                <label
                  style={{
                    fontSize: '0.78rem',
                    marginBottom: '4px',
                    display: 'flex',
                    alignItems: 'center',
                    gap: 6,
                  }}
                >
                  Fax Number
                  <span
                    id="faxNumber-counter"
                    style={{
                      fontSize: '0.72rem',
                      color: faxNumberLen >= MAX.faxNumber ? '#d32f2f' : 'gray',
                    }}
                  >
                    ({faxNumberLen}/{MAX.faxNumber})
                  </span>
                </label>

                <Box sx={{ display: 'flex', gap: 1 }}>
                  <TextField
                    select
                    value={selectedGroupRow?.faxCountryCode || selectedGroupRow?.phoneCountryCode || '1'}
                    onChange={(e) => {
                      const val = (e.target as any).value;
                      setSelectedGroupRow((prev) =>
                        prev ? { ...prev, faxCountryCode: val } : null,
                      );
                    }}
                    size="small"
                    disabled={!isEditable}
                    sx={{
                      ...sharedSx,
                      width: 110,
                      '& .MuiOutlinedInput-root': { height: 30, minHeight: 30 },
                      '& .MuiInputBase-root': { height: 30 },
                      '& .MuiSelect-select': {
                        minHeight: 0,
                        paddingTop: 4,
                        paddingBottom: 4,
                        lineHeight: '30px',
                        fontSize: '0.78rem',
                        display: 'flex',
                        alignItems: 'center',
                      },
                    }}
                    SelectProps={{
                      displayEmpty: true,
                      renderValue: (val) => {
                        const valStr = val as string;
                        const opt =
                          DIAL_CODES.find((o) => o.value === valStr) ||
                          { value: valStr || '', label: valStr || '', countryCode: 'US' };

                        return (
                          <Box sx={{ display: 'flex', alignItems: 'center', gap: 0.5 }}>
                            {!!opt.countryCode && (
                              <ReactCountryFlag
                                countryCode={opt.countryCode}
                                svg
                                style={{ width: '1.1em', height: '1.1em' }}
                              />
                            )}
                            <span>{opt.value}</span>
                          </Box>
                        );
                      },
                      MenuProps: {
                        sx: {
                          '& .MuiMenuItem-root': {
                            fontSize: '0.72rem',
                            minHeight: 30,
                            lineHeight: 1.2,
                          },
                        },
                        PaperProps: { style: { maxHeight: 280 } },
                      },
                    }}
                  >
                    {DIAL_CODES.map((opt) => (
                      <MenuItem key={opt.value} value={opt.value}>
                        <Box sx={{ display: 'flex', alignItems: 'center', gap: 0.75 }}>
                          <ReactCountryFlag
                            countryCode={opt.countryCode}
                            svg
                            style={{ width: '1.1em', height: '1.1em' }}
                          />
                          <span>
                            {opt.label} ({opt.value})
                          </span>
                        </Box>
                      </MenuItem>
                    ))}
                  </TextField>

                  <TextField
                    label=""
                    value={selectedGroupRow?.faxNumber || ''}
                    onChange={(e) => {
                      const formatted = formatPhone(e.target.value);
                      setSelectedGroupRow((prev) =>
                        prev ? { ...prev, faxNumber: formatted } : null,
                      );
                    }}
                    size="small"
                    fullWidth
                    disabled={!isEditable}
                    sx={sharedSx}
                    inputProps={{ maxLength: 13 }}
                  />
                </Box>
              </FormControl>
            </CCol>
          </CRow>
        </CCardBody>
      </CCard>

      {/* Billing / Report Breaks / Search Type / XRef */}
      <CCard
        style={{
          // ✅ FIX 1: don't force height; let content show
          height: 'auto',
          marginBottom: '4px',
          marginTop: '20px',
          border: 'none',
          borderBottom: '1px solid #ccc',
          boxShadow: 'none',
          borderRadius: '0px',
          overflow: 'visible',
        }}
      >
        <CCardBody
          // ✅ FIX 1: remove vertical centering that can crop/hide the last column
          style={{
            padding: '0.25rem 0.5rem',
            backgroundColor: 'transparent',
          }}
        >
          <CRow style={{ marginBottom: 8, marginTop: 4 }}>
            <CCol xs={4}>
              <FormControl fullWidth>
                <label
                  htmlFor="billingsp-input"
                  style={{
                    fontSize: '0.78rem',
                    marginBottom: '4px',
                    display: 'flex',
                    alignItems: 'center',
                    gap: 6,
                  }}
                >
                  Billing Sys/Prin
                  <span
                    id="billingsp-counter"
                    style={{
                      fontSize: '0.72rem',
                      color: billingLen >= MAX.billingSp ? '#d32f2f' : 'gray',
                    }}
                  >
                    ({billingLen}/{MAX.billingSp})
                  </span>
                </label>

                <TextField
                  id="billingsp-input"
                  label=""
                  value={selectedGroupRow?.billingSp || ''}
                  onChange={handleChange('billingSp')}
                  size="small"
                  fullWidth
                  disabled={!isEditable}
                  sx={sharedSx}
                  inputProps={{
                    maxLength: MAX.billingSp,
                    'aria-describedby': 'billingsp-counter',
                    inputMode: 'numeric',
                  }}
                />
              </FormControl>
            </CCol>

            <CCol xs={3}>
              <FormControl fullWidth size="small" sx={sharedSx}>
                <label style={{ fontSize: '0.78rem', marginBottom: '4px' }}>
                  Report Breaks
                </label>
                <Select
                  value={selectedGroupRow?.reportBreakFlag?.toString() || ''}
                  onChange={handleChange('reportBreakFlag')}
                  disabled={!isEditable}
                  displayEmpty
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
                  {REPORT_BREAK_OPTIONS.map((opt) => (
                    <MenuItem key={opt.value} value={opt.value} sx={{ fontSize: '0.78rem' }}>
                      {opt.label}
                    </MenuItem>
                  ))}
                </Select>
              </FormControl>
            </CCol>

            <CCol xs={3}>
              <FormControl fullWidth size="small" sx={sharedSx}>
                <label style={{ fontSize: '0.78rem', marginBottom: '4px' }}>
                  Search Type
                </label>
                <Select
                  value={selectedGroupRow?.chLookUpType?.toString() || ''}
                  onChange={handleChange('chLookUpType')}
                  disabled={!isEditable}
                  displayEmpty
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
                  {SEARCH_TYPE_OPTIONS.map((opt) => (
                    <MenuItem key={opt.value} value={opt.value} sx={{ fontSize: '0.78rem' }}>
                      {opt.label}
                    </MenuItem>
                  ))}
                </Select>
              </FormControl>
            </CCol>

            {/* ✅ XRef - FIXED */}
            <CCol xs={2} style={{ minWidth: 120 }}>
              <FormControl fullWidth>
                <label
                  htmlFor="subClientXref-input"
                  style={{
                    fontSize: '0.78rem',
                    marginBottom: '4px',
                    display: 'flex',
                    alignItems: 'center',
                    gap: 6,
                  }}
                >
                  XRef
                  <span
                    id="subClientXref-counter"
                    style={{
                      fontSize: '0.72rem',
                      color: subClientXrefLen >= MAX.subClientXref ? '#d32f2f' : 'gray',
                    }}
                  >
                    ({subClientXrefLen}/{MAX.subClientXref})
                  </span>
                </label>

                <TextField
                  id="subClientXref-input"
                  label=""
                  // ✅ FIX 2: tolerate null + casing differences
                  value={String(xrefValue ?? '')}
                  onChange={(e) => {
                    const val = e.target.value;

                    // ✅ keep numeric only if you want strict digits (optional)
                    // const val2 = val.replace(/\D/g, '');
                    // if (val2 !== val) return;

                    setSelectedGroupRow((prev) => {
                      if (!prev) return null;
                      // ✅ write both keys to avoid mismatch with backend casing
                      return {
                        ...prev,
                        subClientXref: val,
                        subClientXRef: val,
                      } as any;
                    });
                  }}
                  size="small"
                  fullWidth
                  disabled={!isEditable}
                  sx={sharedSx}
                  inputProps={{
                    maxLength: MAX.subClientXref,
                    'aria-describedby': 'subClientXref-counter',
                    inputMode: 'numeric',
                  }}
                />
              </FormControl>
            </CCol>
          </CRow>
        </CCardBody>
      </CCard>

      {/* Checkboxes */}
      <CCard
        style={{
          height: '90px',
          marginBottom: '4px',
          marginTop: '20px',
          border: 'none',
          borderBottom: '1px solid #ccc',
          boxShadow: 'none',
          borderRadius: '0px',
        }}
      >
        <CCardBody
          className="d-flex align-items-center"
          style={{
            padding: '0.25rem 0.5rem',
            height: '100%',
            backgroundColor: 'transparent',
          }}
        >
          <CRow style={{ marginBottom: '20px' }}>
            <CCol
              xs={3}
              style={{
                display: 'flex',
                alignItems: 'center',
                justifyContent: 'flex-start',
                paddingLeft: '4px',
                flex: '0 0 40%',
                maxWidth: '40%',
              }}
            >
              <FormControlLabel
                control={
                  <Checkbox
                    size="small"
                    checked={!!selectedGroupRow?.active}
                    onChange={handleCheckboxChange('active')}
                    disabled={!isEditable}
                  />
                }
                label={<span style={{ fontSize: '0.78rem' }}>Client Active</span>}
              />
            </CCol>

            <CCol
              xs={3}
              style={{
                display: 'flex',
                alignItems: 'center',
                justifyContent: 'flex-start',
                paddingLeft: '4px',
                flex: '0 0 40%',
                maxWidth: '40%',
              }}
            >
              <FormControlLabel
                control={
                  <Checkbox
                    size="small"
                    checked={!!selectedGroupRow?.positiveReports}
                    onChange={handleCheckboxChange('positiveReports')}
                    disabled={!isEditable}
                  />
                }
                label={<span style={{ fontSize: '0.78rem' }}>Positive Reporting</span>}
              />
            </CCol>

            <CCol
              xs={3}
              style={{
                display: 'flex',
                alignItems: 'center',
                justifyContent: 'flex-start',
                paddingLeft: '4px',
                flex: '0 0 40%',
                maxWidth: '20%',
              }}
            >
              <FormControlLabel
                control={
                  <Checkbox
                    size="small"
                    checked={!!selectedGroupRow?.subClientInd}
                    onChange={handleCheckboxChange('subClientInd')}
                    disabled={!isEditable}
                  />
                }
                label={<span style={{ fontSize: '0.78rem' }}>Sub Client</span>}
              />
            </CCol>

            <CCol
              xs={6}
              style={{
                display: 'flex',
                alignItems: 'center',
                justifyContent: 'flex-start',
                paddingLeft: '4px',
                flex: '0 0 40%',
                maxWidth: '40%',
              }}
            >
              <FormControlLabel
                control={
                  <Checkbox
                    size="small"
                    checked={!!selectedGroupRow?.excludeFromReport}
                    onChange={handleCheckboxChange('excludeFromReport')}
                    disabled={!isEditable}
                  />
                }
                label={<span style={{ fontSize: '0.78rem' }}>Exclude From Postage Reports</span>}
              />
            </CCol>

            <CCol
              xs={6}
              style={{
                display: 'flex',
                alignItems: 'center',
                justifyContent: 'flex-start',
                paddingLeft: '4px',
                flex: '0 0 60%',
                maxWidth: '60%',
              }}
            >
              <FormControlLabel
                control={
                  <Checkbox
                    size="small"
                    checked={!!selectedGroupRow?.amexIssued}
                    onChange={handleCheckboxChange('amexIssued')}
                    disabled={!isEditable}
                  />
                }
                label={<span style={{ fontSize: '0.78rem' }}>Click here If American Express Issued</span>}
              />
            </CCol>
          </CRow>
        </CCardBody>
      </CCard>
    </div>
  );
};

export default EditClientInformation;
