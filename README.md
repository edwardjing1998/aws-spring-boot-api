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

const BAD_ADDRESS_MAP       = { 'Y': 'YES', 'N': 'NO' };
const ACCOUNT_RSCH_MAP       = { 'Y': 'YES', 'N': 'NO' };



const toStr = (v) => (v == null ? '' : String(v));
const asYN  = (v) => (v === true || v === 'Y' ? true : false);
const as10  = (v) => (v === true || v === '1' ? true : false);

const labelOf = (map, value) => map[toStr(value)] ?? '';

const PreviewSubmitInformationA = ({ selectedData, isEditable, sharedSx, getStatusValue }) => {
  // normalized display values
  const specialLabel      = labelOf(SPECIAL_MAP,    selectedData?.special);
  const pinMailerLabel    = labelOf(PIN_MAILER_MAP, selectedData?.pinMailer);
  const destroyLabel      = labelOf(DESTROY_MAP,    selectedData?.destroyStatus);
  const custTypeLabel     = labelOf(CUST_TYPE_MAP,  selectedData?.custType);
  const returnStatusText  = toStr(selectedData?.returnStatus);

  // normalized checkbox booleans
  const badAddressChecked   = as10(selectedData?.addrFlag);
  const accountRschChecked  = as10(selectedData?.astatRch);
  const activeChecked       = as10(selectedData?.sysPrinActive);
  const nm13Checked         = as10(selectedData?.nm13);
  const rpsChecked         = as10(selectedData?.rps);

  const statusOptionsA = ["Destroy", "Return", "Research / Destroy", "Research / Return", "Research / Carrier Ret"];
  const statusOptionsB = ["Destroy", "Return"];
  const statusOptionsC = ["Destroy", "Return", "Research / Destroy", "Research / Return", "Research / Carrier Ret"];
  const statusOptionsD = ["Destroy", "Return", "Research / Destroy", "Research / Return", "Research / Carrier Ret"];
  const statusOptionsE = ["Destroy", "Return", "Research / Destroy", "Research / Return", "Research / Carrier Ret"];
  const statusOptionsF = ["Destroy", "Return", "Research / Destroy", "Research / Return", "Research / Carrier Ret"];
  const statusOptionsI = ["Destroy", "Return", "Research / Destroy", "Research / Return", "Research / Carrier Ret"];
  const statusOptionsL = ["Destroy", "Return"];
  const statusOptionsO = ["Destroy", "Return", "Research / Destroy", "Research / Return", "Research / Carrier Ret"];
  const statusOptionsU = ["Destroy", "Return"];
  const statusOptionsX = ["Destroy", "Return", "Research / Destroy", "Research / Return", "Research / Carrier Ret"];
  const statusOptionsZ = ["Destroy", "Return"];

  const renderStatusRow = (labels, values) => (
      <>
        <CRow style={{ height: '24px', marginBottom: '0px' }}>
          {labels.map(({ code, color }, idx) => (
            <CCol key={idx} style={{ display: 'flex', alignItems: 'center' }}>
              <div style={{
                width: '15px',
                height: '15px',
                backgroundColor: color,
                color: 'white',
                fontWeight: 'bold',
                borderRadius: '3px',
                display: 'flex',
                alignItems: 'center',
                justifyContent: 'center',
                fontSize: '0.78rem',
                marginRight: '5px'
              }}>{code}</div>
              <p style={{ margin: 0, fontSize: '0.78rem' }}>Status</p>
            </CCol>
          ))}
        </CRow>
  
        <CRow style={{ height: '24px', marginTop: '0px' }}>
          {values.map(({ optionList, value }, idx) => (
            <CCol key={idx} style={{ display: 'flex', alignItems: 'center' }}>
              <p  style={{ margin: 0, fontSize: '0.78rem', fontWeight: '800' }}>{getStatusValue(optionList, value)}</p>
            </CCol>
          ))}
        </CRow>
      </>
    );


  // preview-only; if you later want them to edit, pass a setter & update correctly
  const noop = () => {};

  return (
    <>

      <CCard
        style={{
          height: '20px',
          marginBottom: '4px',
          marginTop: '2px',
          border: 'none',
          backgroundColor: '#f3f6f8',
          boxShadow: 'none',
          borderRadius: '4px',
          color: '#0000FF'
        }}
      >
        <CCardBody
          className="d-flex align-items-center"
          style={{
            padding: '0.25rem 0.5rem',
            height: '100%',
            backgroundColor: 'transparent'
          }}
        >
          <p style={{ margin: 0, fontSize: '0.78rem', fontWeight: '500' }}>General</p>
        </CCardBody>
      </CCard>

      {/* Special / Pin Mailer / Destroy */}
      <CCard
        style={{
          // height: '80px',          // ⬅️ remove fixed height
          marginBottom: '4px',
          marginTop: '15px',
          border: 'none',
          boxShadow: 'none',
          borderRadius: '4px',
        }}
      >
        <CCardBody
          style={{
            padding: '0.25rem 0.5rem',
            // height: '100%',        // optional to remove
            backgroundColor: 'white',
            display: 'flex',
            flexDirection: 'column',
            justifyContent: 'flex-start',  // ⬅️ was 'space-between'
            rowGap: '2px',                 // ⬅️ small vertical gap between rows
          }}
        >
          <CRow style={{ height: '20px' }}>
            <CCol style={{ display: 'flex', alignItems: 'center' }}>
              <p style={{ margin: 0, fontSize: '0.78rem' }}>Special</p>
            </CCol>
            <CCol style={{ display: 'flex', alignItems: 'center' }}>
              <p style={{ margin: 0, fontSize: '0.78rem' }}>Pin Mailer</p>
            </CCol>
            <CCol style={{ display: 'flex', alignItems: 'center' }}>
              <p style={{ margin: 0, fontSize: '0.78rem' }}>Destroy Status</p>
            </CCol>

            <CCol style={{ display: 'flex', alignItems: 'center' }}>
              <p style={{ margin: 0, fontSize: '0.78rem' }}>Customer Type</p>
            </CCol>
            <CCol style={{ display: 'flex', alignItems: 'center' }}>
              <p style={{ margin: 0, fontSize: '0.78rem' }}>Return Status</p>
            </CCol>
          </CRow>

          <CRow style={{ height: '20px' }}>
            <CCol style={{ display: 'flex', alignItems: 'center' }}>
              <span style={{ margin: 0, fontSize: '0.78rem', fontWeight: '800' }}>{specialLabel}</span>
            </CCol>

            <CCol style={{ display: 'flex', alignItems: 'center' }}>
              <span style={{ margin: 0, fontSize: '0.78rem', fontWeight: '800' }}>{pinMailerLabel}</span>
            </CCol>

            <CCol style={{ display: 'flex', alignItems: 'center' }}>
              <span style={{ margin: 0, fontSize: '0.78rem', fontWeight: '800' }}>{destroyLabel}</span>
            </CCol>

            <CCol style={{ display: 'flex', alignItems: 'center' }}>
              <span style={{ margin: 0, fontSize: '0.78rem', fontWeight: '800' }}>{custTypeLabel}</span>
            </CCol>
            <CCol style={{ display: 'flex', alignItems: 'center' }}>
              <span style={{ margin: 0, fontSize: '0.78rem', fontWeight: '800' }}>{returnStatusText}</span>
            </CCol>

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
            <CCol xs={3} style={{ display: 'flex', alignItems: 'center', justifyContent: 'flex-start', paddingLeft: '4px', flex: '0 0 40%', maxWidth: '40%' }}>
              <FormControlLabel
                control={<Checkbox size="small" checked={badAddressChecked} onChange={noop} disabled />}
                label="Flag Undeliverable as Invalid Address"
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

            <CCol xs={3}  style={{ display: 'flex', alignItems: 'center', justifyContent: 'flex-start', paddingLeft: '4px', flex: '0 0 35%', maxWidth: '35%' }}>
              <FormControlLabel
                control={<Checkbox size="small" checked={accountRschChecked} onChange={noop} disabled />}
                label="A Status Accounts Going in Research"
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

            <CCol xs={3} style={{ display: 'flex', alignItems: 'center', justifyContent: 'flex-start', paddingLeft: '4px', flex: '0 0 25%', maxWidth: '25%' }}>
              <FormControlLabel
                control={<Checkbox size="small" checked={rpsChecked} onChange={noop} disabled />}
                label="RPS Customer"
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
                control={<Checkbox size="small" checked={activeChecked} onChange={noop} disabled />}
                label="SysPrin Active"
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

            <CCol xs={3} style={{ display: 'flex', alignItems: 'center', justifyContent: 'flex-start', paddingLeft: '4px', flex: '0 0 55%', maxWidth: '55%' }}>
              <FormControlLabel
                control={<Checkbox size="small" checked={nm13Checked} onChange={noop} disabled />}
                label="Perform Non-Mon 13 on Destroy"
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
      <div style={{ overflow: 'visible' }}>
              <CCard
                style={{
                  height: '20px',
                  marginBottom: '4px',
                  marginTop: '2px',
                  border: 'none',
                  backgroundColor: '#f3f6f8',
                  boxShadow: 'none',
                  borderRadius: '4px',
                  color: '#0000FF'
                }}
              >
                <CCardBody
                  className="d-flex align-items-center"
                  style={{
                    padding: '0.25rem 0.5rem',
                    height: '100%',
                    backgroundColor: 'transparent'
                  }}
                >
                  <p style={{ margin: 0, fontSize: '0.78rem', fontWeight: '500' }}>Status Options</p>
                </CCardBody>
              </CCard>
      
              <CCard style={{ marginBottom: '0px', marginTop: '5px', minHeight: '50px', border: 'none', boxShadow: 'none', borderBottom: '1px solid #ccc'}}>
                <CCardBody style={{
                  padding: '0.25rem 0.5rem',
                  backgroundColor: 'white',
                  display: 'flex',
                  flexDirection: 'column',
                  justifyContent: 'flex-start',
                  gap: '0px',     
                }}>
                  {renderStatusRow(
                    [
                      { code: 'A', color: 'red' },
                      { code: 'B', color: 'brown' },
                      { code: 'C', color: 'green' },
                      { code: 'E', color: 'black' },
                      { code: 'D', color: '#8410f1' },
                      { code: 'O', color: 'blue' },
                    ],
                    [
                      { optionList: statusOptionsA, value: selectedData?.statA },
                      { optionList: statusOptionsB, value: selectedData?.statB },
                      { optionList: statusOptionsC, value: selectedData?.statC },
                      { optionList: statusOptionsE, value: selectedData?.statE },
                      { optionList: statusOptionsD, value: selectedData?.statD },
                      { optionList: statusOptionsO, value: selectedData?.statO },
                    ]
                  )}
                </CCardBody>
              </CCard>
      
              <CCard style={{ marginBottom: '0px', marginTop: '5px', minHeight: '50px', border: 'none', boxShadow: 'none', borderBottom: '1px solid #ccc', }}>
                <CCardBody style={{
                  padding: '0.25rem 0.5rem',
                  backgroundColor: 'white',
                  display: 'flex',
                  flexDirection: 'column',
                  justifyContent: 'flex-start',
                  gap: '0px'
                }}>
                  {renderStatusRow(
                    [
                      { code: 'F', color: 'blue' },
                      { code: 'I', color: '#f110ee' },
                      { code: 'L', color: '#f17610' },
                      { code: 'U', color: '#10e7f1' },
                      { code: 'X', color: '#65167c' },
                      { code: 'Z', color: '#c07bc4' }
                    ],
                    [
                      { optionList: statusOptionsF, value: selectedData?.statF },
                      { optionList: statusOptionsI, value: selectedData?.statI },
                      { optionList: statusOptionsL, value: selectedData?.statL },
                      { optionList: statusOptionsU, value: selectedData?.statU },
                      { optionList: statusOptionsX, value: selectedData?.statX },
                      { optionList: statusOptionsZ, value: selectedData?.statZ }
                    ]
                  )}
                </CCardBody>
              </CCard>
            </div>
    </>
  );
};

export default PreviewSubmitInformationA;
