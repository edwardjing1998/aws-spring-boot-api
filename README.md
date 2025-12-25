import React, { useState } from 'react';
import { CRow, CCol, CCard, CCardBody } from '@coreui/react';
import { Tabs, Tab, Box, TextField, FormControl, FormControlLabel, Checkbox } from '@mui/material';
import PreviewClientEmails from '../emails/PreviewClientEmails';
import PreviewAtmAndCashPrefix from '../atm-cash-prefix/PreviewAtmAndCashPrefix';
import PreviewClientReports from '../reports/PreviewClientReports';
import PreviewClientSysPrinList from './PreviewClientSysPrinList';
import { REPORT_BREAK_OPTIONS, SEARCH_TYPE_OPTIONS } from '../utils/ClientInfoFieldValueMapping';

// --- Interfaces ---

export interface ClientGroupRow {
  client?: string;
  name?: string;
  addr?: string;
  city?: string;
  state?: string;
  zip?: string;
  contact?: string;
  phone?: string;
  phoneCountryCode?: string;
  active?: boolean;
  faxNumber?: string;
  billingSp?: string;
  reportBreakFlag?: string | number;
  chLookUpType?: string | number;
  excludeFromReport?: boolean;
  positiveReports?: boolean;
  subClientInd?: boolean;
  subClientXref?: string;
  amexIssued?: boolean;
  sysPrins?: any[]; 
  sysPrinTotal?: number;
  reportOptions?: any[]; 
  reportOptionTotal?: number;
  sysPrinsPrefixes?: any[]; 
  clientPrefixTotal?: number;
  clientEmail?: any[];
  clientEmailTotal?: number;
  [key: string]: any;
}

interface PreviewClientInformationProps {
  // ✅ FIX: Update type to match the state setter from ClientInformationPage
  setClientInformationWindow?: React.Dispatch<React.SetStateAction<{
    open: boolean;
    mode: 'edit' | 'new' | 'delete';
  }>>;
  selectedGroupRow: ClientGroupRow | null;
}

const PreviewClientInformation: React.FC<PreviewClientInformationProps> = ({
  setClientInformationWindow,
  selectedGroupRow,
}) => {
  // eslint-disable-next-line @typescript-eslint/no-unused-vars
  const [isEditable] = useState<boolean>(false);
  const [tabIndex, setTabIndex] = useState<number>(0);

  const handleTabChange = (_event: React.SyntheticEvent, newValue: number) => {
    setTabIndex(newValue);
  };

  const sharedSx = {
    '& .MuiInputBase-root': {
      height: '38px',       // outer container
      fontSize: '0.78rem',
    },
    '& .MuiInputBase-input': {
      padding: '8px 10px',  // more padding for taller field
      height: '22px',       // input line height
      fontSize: '0.78rem',
      lineHeight: '1.2rem',
    },
    '& .MuiInputLabel-root': {
      fontSize: '0.78rem',
      lineHeight: '1.2rem',
    },
    '& .MuiInputBase-input.Mui-disabled': {
      color: 'black',
      WebkitTextFillColor: 'black',
    },
    '& .MuiInputLabel-root.Mui-disabled': {
      color: 'black',
    },
  };
  
  return (
    <>
      <Box sx={{ borderBottom: 1, borderColor: 'divider', marginBottom: '10px' }}>
        <Tabs value={tabIndex} onChange={handleTabChange} variant="scrollable" scrollButtons="auto">
          <Tab label="General Info" sx={{ textTransform: 'none' }} />
          <Tab label="Sys/Prin List" sx={{ textTransform: 'none' }} />
          <Tab label="Client Reports" sx={{ textTransform: 'none' }} />
          <Tab label="ATM Prefixes" sx={{ textTransform: 'none' }} />
          <Tab label="Client Emails" sx={{ textTransform: 'none' }} />
        </Tabs>
      </Box>

      {tabIndex === 0 && (
        <>
          <CCard style={{ height: '70px', marginBottom: '4px', marginTop: '15px', boxShadow: 'none', border: 'none', backgroundColor: 'white' }}>
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
            <CRow style={{ height: '35px' }}>
              <CCol style={{ flex: '0 0 25%' }}>
                <p style={{ margin: 0, fontSize: '0.78rem' }}>Phone</p>
              </CCol>
              <CCol style={{ flex: '0 0 25%' }}>
                <p style={{ margin: 0, fontSize: '0.78rem'  }}>Fax Number</p>
              </CCol>
              <CCol style={{ flex: '0 0 25%' }}>
                <p style={{ margin: 0, fontSize: '0.78rem' }}>Contact</p>
              </CCol>

              <CCol style={{ flex: '0 0 3%' }}>
              </CCol>

              {/* Address column, right-aligned */}
              <CCol
                style={{
                  textAlign: 'left', 
                  display: 'flex',
                  flexDirection: 'column',
                  justifyContent: 'center',
                  flex: '0 0 20%' 
                }}
              >
                {/* Address row – allow wrapping */}
                <CRow style={{ minHeight: '20px' }}>
                  <CCol
                    style={{
                      flex: '0 0 100%',
                      fontSize: '0.78rem',
                      textAlign: 'left',
                      whiteSpace: 'normal',      // allow wrapping
                      wordBreak: 'break-word',   // break long words if needed
                      lineHeight: '1.1rem',
                      color: '#2554C7'
                    }}
                  >
                    {selectedGroupRow?.addr || 'xxx'}
                  </CCol>
                </CRow>

                {/* City / State row – can be fixed height or also minHeight if you want wrapping */}
                <CRow style={{ height: '20px' }}>
                  <CCol
                    style={{
                      flex: '0 0 100%',
                      fontSize: '0.78rem',
                      textAlign: 'left',
                      whiteSpace: 'nowrap',      // keep on one line (optional)
                      overflow: 'hidden',
                      textOverflow: 'ellipsis',
                      color: '#2554C7'
                    }}
                  >
                    {selectedGroupRow?.city || 'xxx'}, {selectedGroupRow?.state || 'xx'}
                  </CCol>
                </CRow>

                {/* Zip row with bottom border – stays properly aligned */}
                <CRow style={{ height: '20px', borderBottom: '1px solid #2554C7' }}>
                  <CCol
                    style={{
                      flex: '0 0 100%',
                      fontSize: '0.78rem',
                      textAlign: 'left',
                      color: '#2554C7'
                    }}
                  >
                    {selectedGroupRow?.zip || 'xxx'}
                  </CCol>
                </CRow>
              </CCol>
              <CCol style={{ flex: '0 0 2%', fontSize: '0.78rem', textAlign: 'left' }}>
              </CCol>
            </CRow>

              <CRow style={{ height: '35px' }}>
                <CCol>
                  <TextField placeholder="xxx" value={selectedGroupRow?.phone || 'xxx'} size="small" fullWidth disabled={!isEditable} sx={sharedSx} />
                </CCol>
                <CCol>
                   <TextField placeholder="xxx" value={selectedGroupRow?.faxNumber || 'xxx'} size="small" fullWidth disabled={!isEditable} sx={sharedSx} />
                </CCol>
                <CCol>
                   <TextField placeholder="xxx" value={selectedGroupRow?.contact || 'xxx'} size="small" fullWidth disabled={!isEditable} sx={sharedSx} />
                </CCol>
                <CCol>
                </CCol>
              </CRow>
            </CCardBody>
          </CCard>

          <CCard style={{ height: '70px', marginBottom: '4px', marginTop: '15px', boxShadow: 'none', border: 'none', backgroundColor: 'white' }}>
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
              <CRow style={{ height: '35px' }}>
                <CCol><p style={{ margin: 0, fontSize: '0.78rem' }}>Billing Sys/Prin</p></CCol>
                <CCol><p style={{ margin: 0, fontSize: '0.78rem' }}>Report Breaks</p></CCol>
                <CCol><p style={{ margin: 0, fontSize: '0.78rem' }}>Search Type</p></CCol>
                <CCol><p style={{ margin: 0, fontSize: '0.78rem' }}>Client ID XRef</p></CCol>
              </CRow>

              <CRow style={{ height: '35px' }}>
                <CCol>
                  <TextField placeholder="xxx" value={selectedGroupRow?.billingSp || ''} size="small" fullWidth disabled={!isEditable} sx={sharedSx} />
                </CCol>
                <CCol>
                  <TextField
                    placeholder="xxx"
                    value={REPORT_BREAK_OPTIONS.find(opt => opt.value === String(selectedGroupRow?.reportBreakFlag ?? ''))?.label || ''}
                    size="small"
                    fullWidth
                    disabled={!isEditable}
                    sx={sharedSx}
                  />
                </CCol>
                <CCol>
                  <TextField
                    placeholder="xxx"
                    value={SEARCH_TYPE_OPTIONS.find(opt => opt.value === String(selectedGroupRow?.chLookUpType ?? ''))?.label || ''}
                    size="small"
                    fullWidth
                    disabled={!isEditable}
                    sx={sharedSx}
                  />
                </CCol>
                <CCol>
                  <TextField placeholder="xxx" value={selectedGroupRow?.subClientXref || ''} size="small" fullWidth disabled={!isEditable} sx={sharedSx} />
                </CCol>
              </CRow>
            </CCardBody>
          </CCard>

          <CCard style={{ marginTop: '25px', marginBottom: '10px' }}>
            <CCardBody
              style={{
                padding: '0.8rem',
                backgroundColor: 'white',
                display: 'flex',
                flexDirection: 'column',
                rowGap: '0px',
              }}
            >
              <CRow style={{ height: '35px' }}>
                <CCol style={{ flex: '0 0 40%' }}>
                  <FormControlLabel
                    control={<Checkbox size="small" checked={selectedGroupRow?.active || false} disabled={!isEditable} />}
                    label="Client Active"
                    sx={{ pl: 1, m: 0, '& .MuiFormControlLabel-label': { fontSize: '0.78rem', color: 'black' } }}
                  />
                </CCol>
                <CCol style={{ flex: '0 0 40%' }}>
                  <FormControlLabel
                    control={<Checkbox size="small" checked={selectedGroupRow?.positiveReports || false} disabled={!isEditable} />}
                    label="Positive Reporting"
                    sx={{ pl: 1, m: 0, '& .MuiFormControlLabel-label': { fontSize: '0.78rem', color: 'gray' } }}
                  />
                </CCol>
                <CCol style={{ flex: '0 0 20%', fontSize: '0.78rem' }}>
                </CCol>
              </CRow>

              <CRow style={{ height: '35px' }}>

                <CCol style={{ flex: '0 0 40%' }}>
                  <FormControlLabel
                    control={<Checkbox size="small" checked={selectedGroupRow?.excludeFromReport || false} disabled={!isEditable} />}
                    label="Exclude From Postage Reports"
                    sx={{ pl: 1, m: 0, '& .MuiFormControlLabel-label': { fontSize: '0.78rem', color: 'black' } }}
                  />
                </CCol>

                <CCol style={{ flex: '0 0 40%' }}>
                  <FormControlLabel
                    control={<Checkbox size="small" checked={selectedGroupRow?.subClientInd || false} disabled={!isEditable} />}
                    label="Sub Client"
                    sx={{ pl: 1, m: 0, '& .MuiFormControlLabel-label': { fontSize: '0.78rem', color: 'black' } }}
                  />
                </CCol>
              </CRow>

              <CRow style={{ height: '35px' }}>
                <CCol style={{ flex: '0 0 40%' }}>
                  <FormControlLabel
                    control={<Checkbox size="small" checked={selectedGroupRow?.amexIssued || false} disabled={!isEditable} />}
                    label="Check Here: If American Express Issued"
                    sx={{ pl: 1, m: 0, '& .MuiFormControlLabel-label': { fontSize: '0.78rem', color: 'black' } }}
                  />
                </CCol>
                <CCol style={{ flex: '0 0 30%' }}>
                </CCol>
              </CRow>
            </CCardBody>
          </CCard>
        </>
      )}

      {tabIndex === 1 && (
        <>
         <CCard
            style={{
              height: '35px',
              marginBottom: '4px',
              marginTop: '2px',
              border: 'none',                   // remove border
              backgroundColor: '#f3f6f8',       // very light green background
              boxShadow: 'none',                // remove any shadow
              borderRadius: '4px'               // optional: slight rounding
            }}
          >
            <CCardBody
              className="d-flex align-items-center"
              style={{
                padding: '0.25rem 0.5rem',
                height: '100%',
                backgroundColor: 'transparent' // ensure it inherits from card
              }}
            >
              <p
                style={{
                  margin: 0,
                  fontSize: '0.78rem',
                  fontWeight: '500'
                }}
              >
                {selectedGroupRow
                  ? `Selected Client: ${selectedGroupRow.client} - ${selectedGroupRow.name || ''}`
                  : 'No Client Found'}
              </p>
            </CCardBody>
          </CCard>

          <CCard style={{ height: '250px', marginBottom: '4px' }}>
            <CCardBody style={{ padding: '0.25rem 0.5rem' }}>
              <PreviewClientSysPrinList 
              data={selectedGroupRow?.sysPrins || []} 
              sysPrinTotal={selectedGroupRow?.sysPrinTotal} />
            </CCardBody>
          </CCard>
        </>
      )}

      {tabIndex === 2 && (
        <>
         <CCard
            style={{
              height: '35px',
              marginBottom: '4px',
              marginTop: '2px',
              border: 'none',                   // remove border
              backgroundColor: '#f3f6f8',       // very light green background
              boxShadow: 'none',                // remove any shadow
              borderRadius: '4px'               // optional: slight rounding
            }}
          >
            <CCardBody
              className="d-flex align-items-center"
              style={{
                padding: '0.25rem 0.5rem',
                height: '100%',
                backgroundColor: 'transparent' // ensure it inherits from card
              }}
            >
              <p
                style={{
                  margin: 0,
                  fontSize: '0.78rem',
                  fontWeight: '500'
                }}
              >
                {selectedGroupRow
                  ? `Selected Client: ${selectedGroupRow.client} - ${selectedGroupRow.name || ''}`
                  : 'No Client Found'}
              </p>
            </CCardBody>
          </CCard>

          <CCard style={{ height: '250px', marginBottom: '4px' }}>
            <CCardBody style={{ padding: '0.25rem 0.5rem' }}>
              <PreviewClientReports
                clientId={clientId} 
                data={selectedGroupRow?.reportOptions || []} 
                reportOptionTotal={selectedGroupRow?.reportOptionTotal} 
              />
            </CCardBody>
          </CCard>
        </>
      )}

      {tabIndex === 3 && (
        <>
         <CCard
            style={{
              height: '35px',
              marginBottom: '4px',
              marginTop: '2px',
              border: 'none',                   // remove border
              backgroundColor: '#f3f6f8',       // very light green background
              boxShadow: 'none',                // remove any shadow
              borderRadius: '4px'               // optional: slight rounding
            }}
          >
            <CCardBody
              className="d-flex align-items-center"
              style={{
                padding: '0.25rem 0.5rem',
                height: '100%',
                backgroundColor: 'transparent' // ensure it inherits from card
              }}
            >
              <p
                style={{
                  margin: 0,
                  fontSize: '0.78rem',
                  fontWeight: '500'
                }}
              >
                {selectedGroupRow
                  ? `Selected Client: ${selectedGroupRow.client} - ${selectedGroupRow.name || ''}`
                  : 'No Client Found'}
              </p>
            </CCardBody>
          </CCard>

          <CCard style={{ height: '250px', marginBottom: '4px' }}>
            <CCardBody style={{ padding: '0.25rem 0.5rem' }}>
              <PreviewAtmAndCashPrefix 
                data={selectedGroupRow?.sysPrinsPrefixes || []} 
                clientPrefixTotal={selectedGroupRow?.clientPrefixTotal} />
            </CCardBody>
          </CCard>
        </>
      )}

      {tabIndex === 4 && (
        <>
         <CCard
            style={{
              height: '35px',
              marginBottom: '4px',
              marginTop: '2px',
              border: 'none',                   // remove border
              backgroundColor: '#f3f6f8',       // very light green background
              boxShadow: 'none',                // remove any shadow
              borderRadius: '4px'               // optional: slight rounding
            }}
          >
            <CCardBody
              className="d-flex align-items-center"
              style={{
                padding: '0.25rem 0.5rem',
                height: '100%',
                backgroundColor: 'transparent' // ensure it inherits from card
              }}
            >
              <p
                style={{
                  margin: 0,
                  fontSize: '0.78rem',
                  fontWeight: '500'
                }}
              >
                {selectedGroupRow
                  ? `Selected Client: ${selectedGroupRow.client} - ${selectedGroupRow.name || ''}`
                  : 'No Client Found'}
              </p>
            </CCardBody>
          </CCard>

          <CCard style={{ height: '250px', marginBottom: '4px' }}>
            <CCardBody style={{ padding: '0.25rem 0.5rem' }}>
              <PreviewClientEmails 
                data={selectedGroupRow?.clientEmail || []} 
                clientEmailTotal={selectedGroupRow?.clientEmailTotal} />
            </CCardBody>
          </CCard>
        </>
      )}
    </>
  );
};

export default PreviewClientInformation;




ERROR in src/Client/components/PreviewClientInformation.tsx:401:27
TS2304: Cannot find name 'clientId'.
    399 |             <CCardBody style={{ padding: '0.25rem 0.5rem' }}>
    400 |               <PreviewClientReports
  > 401 |                 clientId={clientId}
        |                           ^^^^^^^^
    402 |                 data={selectedGroupRow?.reportOptions || []}
    403 |                 reportOptionTotal={selectedGroupRow?.reportOptionTotal}
    404 |               />
