import React from 'react';
import { CCard, CCardBody, CRow, CCol } from '@coreui/react';
import TextField from '@mui/material/TextField';
import FormControlLabel from '@mui/material/FormControlLabel';
import Checkbox from '@mui/material/Checkbox';

// Code -> Label maps (match what SysPrinGeneral uses)
const SPECIAL_MAP       = { '0': 'None', '1': 'Destroy', '2': 'Return' };
const PIN_MAILER_MAP    = { '0': 'Non',  '1': 'Destroy', '2': 'Return' };
const DESTROY_MAP       = { '0': 'None', '1': 'Destroy', '2': 'Return' };
const CUST_TYPE_MAP     = { '0': 'None', '1': 'Full Processing', '2': 'Destroy All', '3': 'Return All' };

const toStr = (v) => (v == null ? '' : String(v));
const asYN  = (v) => (v === true || v === 'Y' ? true : false);
const as10  = (v) => (v === true || v === '1' ? true : false);

const labelOf = (map, value) => map[toStr(value)] ?? '';

const PreviewGeneralInformation = ({ selectedData, isEditable, sharedSx }) => {
  // normalized display values
  const specialLabel      = labelOf(SPECIAL_MAP,    selectedData?.special);
  const pinMailerLabel    = labelOf(PIN_MAILER_MAP, selectedData?.pinMailer);
  const destroyLabel      = labelOf(DESTROY_MAP,    selectedData?.destroyStatus);
  const custTypeLabel     = labelOf(CUST_TYPE_MAP,  selectedData?.custType);
  const returnStatusText  = toStr(selectedData?.returnStatus);

  // normalized checkbox booleans
  const badAddressChecked   = asYN(selectedData?.addrFlag);
  const accountRschChecked  = as10(selectedData?.astatRch);
  const activeChecked       = asYN(selectedData?.sysPrinActive);
  const nm13Checked         = as10(selectedData?.nm13);

  // preview-only; if you later want them to edit, pass a setter & update correctly
  const noop = () => {};

  return (
    <>
      {/* General header */}
      <CCard style={{ height: '35px', marginBottom: '4px', marginTop: '2px', border: 'none', backgroundColor: '#f3f6f8', boxShadow: 'none', borderRadius: '4px' }}>
        <CCardBody className="d-flex align-items-center" style={{ padding: '0.25rem 0.5rem', height: '100%', backgroundColor: 'transparent' }}>
          <p style={{ margin: 0, fontSize: '0.78rem', fontWeight: 500 }}>General</p>
        </CCardBody>
      </CCard>

      {/* Special / Pin Mailer / Destroy */}
      <CCard style={{ height: '80px', marginBottom: '4px', marginTop: '15px', border: 'none', boxShadow: 'none', borderRadius: '4px' }}>
        <CCardBody style={{ padding: '0.25rem 0.5rem', height: '100%', backgroundColor: 'white', display: 'flex', flexDirection: 'column', justifyContent: 'space-between' }}>
          <CRow style={{ height: '25px' }}>
            <CCol style={{ display: 'flex', alignItems: 'center' }}>
              <p style={{ margin: 0, fontSize: '0.78rem' }}>Special</p>
            </CCol>
            <CCol style={{ display: 'flex', alignItems: 'center' }}>
              <p style={{ margin: 0, fontSize: '0.78rem' }}>Pin Mailer</p>
            </CCol>
            <CCol style={{ display: 'flex', alignItems: 'center' }}>
              <p style={{ margin: 0, fontSize: '0.78rem' }}>Destroy Status</p>
            </CCol>
          </CRow>

          <CRow style={{ height: '25px' }}>
            <CCol style={{ display: 'flex', alignItems: 'center' }}>
              <TextField
                placeholder="xxx"
                value={specialLabel}
                size="small"
                fullWidth
                disabled
                sx={{
                  ...sharedSx,
                  marginTop: '-12px',
                  '& .MuiInputBase-root': { minHeight: '25px', fontSize: '0.78rem', padding: '0px 0px' },
                  '& input': { padding: '0px 0px' },
                }}
              />
            </CCol>

            <CCol style={{ display: 'flex', alignItems: 'center' }}>
              <TextField
                placeholder="xxx"
                value={pinMailerLabel}
                size="small"
                fullWidth
                disabled
                sx={{
                  ...sharedSx,
                  marginTop: '-12px',
                  '& .MuiInputBase-root': { minHeight: '25px', fontSize: '0.78rem', padding: '0px 0px' },
                  '& input': { padding: '0px 0px' },
                }}
              />
            </CCol>

            <CCol style={{ display: 'flex', alignItems: 'center' }}>
              <TextField
                placeholder="xxx"
                value={destroyLabel}
                size="small"
                fullWidth
                disabled
                sx={{
                  ...sharedSx,
                  marginTop: '-12px',
                  '& .MuiInputBase-root': { minHeight: '25px', fontSize: '0.78rem', padding: '0px 0px' },
                  '& input': { padding: '0px 0px' },
                }}
              />
            </CCol>
          </CRow>
        </CCardBody>
      </CCard>

      {/* Customer Type / Return Status */}
      <CCard
        style={{
          marginTop: '30px',
          height: '80px',
          marginBottom: '4px',
          border: 'none',
          boxShadow: 'none',
          borderTop: '1px solid #ccc',
          borderRadius: '4px',
        }}
      >
        <CCardBody
          style={{
            padding: '0.25rem 0.5rem',
            height: '100%',
            backgroundColor: 'white',
            display: 'flex',
            flexDirection: 'column',
            justifyContent: 'space-between',
          }}
        >
          <CRow style={{ height: '25px' }}>
            <CCol style={{ display: 'flex', alignItems: 'center' }}>
              <p style={{ margin: 0, fontSize: '0.78rem' }}>Customer Type</p>
            </CCol>
            <CCol style={{ display: 'flex', alignItems: 'center' }}>
              <p style={{ margin: 0, fontSize: '0.78rem' }}>Return Status</p>
            </CCol>
            <CCol />
          </CRow>

          <CRow style={{ height: '25px' }}>
            <CCol style={{ display: 'flex', alignItems: 'center' }}>
              <TextField
                placeholder="0"
                value={custTypeLabel}     // <-- correct label for the code
                size="small"
                fullWidth
                disabled
                sx={{
                  ...sharedSx,
                  marginTop: '-12px',
                  '& .MuiInputBase-root': { minHeight: '25px', fontSize: '0.78rem', padding: '0px 0px' },
                  '& input': { padding: '0px 0px' },
                }}
              />
            </CCol>

            <CCol style={{ display: 'flex', alignItems: 'center' }}>
              <TextField
                placeholder="xxx"
                value={returnStatusText}
                size="small"
                fullWidth
                disabled
                sx={{
                  ...sharedSx,
                  marginTop: '-12px',
                  '& .MuiInputBase-root': { minHeight: '25px', fontSize: '0.78rem', padding: '0px 0px' },
                  '& input': { padding: '0px 0px' },
                }}
              />
            </CCol>
            <CCol />
          </CRow>
        </CCardBody>
      </CCard>

      {/* Flags */}
      <CCard style={{ marginTop: '30px', marginBottom: '10px', height: '100px' }}>
        <CCardBody
          style={{
            padding: '0.8rem',
            backgroundColor: 'white',
            display: 'flex',
            flexDirection: 'column',
            rowGap: '0px',
            height: '100%',
          }}
        >
          <CRow style={{ height: '30px' }}>
            <CCol xs={3} style={{ display: 'flex', alignItems: 'center', justifyContent: 'flex-start', paddingLeft: '4px', flex: '0 0 41%', maxWidth: '41%' }}>
              <FormControlLabel
                control={<Checkbox size="small" checked={badAddressChecked} onChange={noop} disabled />}
                label="Bad Address"
                sx={{
                  backgroundColor: 'white',
                  pl: 1,
                  m: 0,
                  '& .MuiFormControlLabel-label': { fontSize: '0.78rem', color: 'black' },
                  '& .Mui-disabled + .MuiFormControlLabel-label': { color: 'black' },
                  paddingLeft: '0px',
                  paddingRight: '0px',
                }}
              />
            </CCol>

            <CCol xs={3}  style={{ display: 'flex', alignItems: 'center', justifyContent: 'flex-start', paddingLeft: '4px', flex: '0 0 59%', maxWidth: '59%' }}>
              <FormControlLabel
                control={<Checkbox size="small" checked={accountRschChecked} onChange={noop} disabled />}
                label="Account Research"
                sx={{
                  backgroundColor: 'white',
                  pl: 1,
                  m: 0,
                  paddingLeft: '2px',
                  paddingRight: '0px',
                  '& .MuiFormControlLabel-label': { fontSize: '0.78rem', color: 'black' },
                  '& .Mui-disabled + .MuiFormControlLabel-label': { color: 'black' },
                }}
              />
            </CCol>

            <CCol xs={3} style={{ display: 'flex', alignItems: 'center', justifyContent: 'flex-start', paddingLeft: '4px', flex: '0 0 58%', maxWidth: '58%' }}>
              <FormControlLabel
                control={<Checkbox size="small" checked={nm13Checked} onChange={noop} disabled />}
                label="Non-Mon"
                sx={{
                  backgroundColor: 'white',
                  pl: 1,
                  m: 0,
                  paddingLeft: '0px',
                  paddingRight: '0px',
                  '& .MuiFormControlLabel-label': { fontSize: '0.78rem', color: 'black' },
                  '& .Mui-disabled + .MuiFormControlLabel-label': { color: 'black' },
                }}
              />
            </CCol>
          </CRow>

          <CRow style={{ height: '30px' }}>
            <CCol xs={3} style={{ display: 'flex', alignItems: 'center', justifyContent: 'flex-start', paddingLeft: '4px', flex: '0 0 42%', maxWidth: '42%' }}>
              <FormControlLabel
                control={<Checkbox size="small" checked={activeChecked} onChange={noop} disabled />}
                label="Active"
                sx={{
                  backgroundColor: 'white',
                  pl: 1,
                  m: 0,
                  paddingLeft: '0px',
                  paddingRight: '0px',
                  '& .MuiFormControlLabel-label': { fontSize: '0.78rem', color: 'black' },
                  '& .Mui-disabled + .MuiFormControlLabel-label': { color: 'black' },
                }}
              />
            </CCol>

            <CCol xs={3} style={{ display: 'flex', alignItems: 'center', justifyContent: 'flex-start', paddingLeft: '4px', flex: '0 0 58%', maxWidth: '58%' }}>
              <FormControlLabel
                control={<Checkbox size="small" checked={nm13Checked} onChange={noop} disabled />}
                label="Non-Mon 13 on Destroy"
                sx={{
                  backgroundColor: 'white',
                  pl: 1,
                  m: 0,
                  paddingLeft: '0px',
                  paddingRight: '0px',
                  '& .MuiFormControlLabel-label': { fontSize: '0.78rem', color: 'black' },
                  '& .Mui-disabled + .MuiFormControlLabel-label': { color: 'black' },
                }}
              />
            </CCol>
          </CRow>
        </CCardBody>
      </CCard>
    </>
  );
};

export default PreviewGeneralInformation;
