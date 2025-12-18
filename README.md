// PreviewStatusOptions.js
import React, { useState } from 'react';
import { CCard, CCardBody, CRow, CCol, CButton } from '@coreui/react';
import TextField from '@mui/material/TextField';

import {
  unableToDeliver,
  forwardingAddress,
  nonUS,
  invalidState,
  isPOBox,
} from '../../utils/ClientInfoFieldValueMapping';

const PreviewReMailOptions = ({ selectedData, sharedSx }) => {
  const [isEditable, setIsEditable] = useState(false);

  const getStatusValue = (options, code) => {
    if (!code) return '';
    const match = options.find((opt) => opt.code === code);
    return match ? match.label : '';
  };

  const fieldSx = {
    ...sharedSx,
    '& .MuiInputBase-root': {
      height: '36px',
      fontSize: '0.78rem',
    },
    '& input': {
      padding: '8px 12px',
    },
  };

  return (
    <>
      <CCard style={{ height: '35px', marginBottom: '4px', marginTop: '2px', border: 'none', backgroundColor: '#f3f6f8', boxShadow: 'none', borderRadius: '4px' }}>
        <CCardBody className="d-flex align-items-center" style={{ padding: '0.25rem 0.5rem', height: '100%', backgroundColor: 'transparent' }}>
          <p style={{ margin: 0, fontSize: '0.78rem', fontWeight: '500' }}>General</p>
        </CCardBody>
      </CCard>

      {/* Hold Days / Temp Aways / Attempts */}
      <CCard style={{ margin: '20px 0 0', minHeight: '100px', border: 'none', boxShadow: 'none', borderBottom: '1px solid #ccc' }}>
        <CCardBody style={{ padding: '0.25rem 0.5rem', backgroundColor: 'white', display: 'flex', flexDirection: 'column', rowGap: '4px' }}>
          <CRow style={{ height: '25px' }}>
            <CCol><p style={{ margin: 0, fontSize: '0.78rem' }}>Hold Days</p></CCol>
            <CCol><p style={{ margin: 0, fontSize: '0.78rem' }}>Temp Aways</p></CCol>
            <CCol><p style={{ margin: 0, fontSize: '0.78rem' }}>Attempts</p></CCol>
          </CRow>
          <CRow style={{ height: '25px' }}>
            <CCol><TextField placeholder="0" value={selectedData?.holdDays} size="small" fullWidth disabled={!isEditable} sx={fieldSx} /></CCol>
            <CCol><TextField placeholder="0" value={selectedData?.tempAway} size="small" fullWidth disabled={!isEditable} sx={fieldSx} /></CCol>
            <CCol><TextField placeholder="0" value={selectedData?.tempAwayAtts} size="small" fullWidth disabled={!isEditable} sx={fieldSx} /></CCol>
          </CRow>
        </CCardBody>
      </CCard>

      {/* Unable Deliver / FWD Address / Non-US */}
      <CCard style={{ margin: '20px 0 0', minHeight: '100px', border: 'none', boxShadow: 'none', borderBottom: '1px solid #ccc' }}>
        <CCardBody style={{ padding: '0.25rem 0.5rem', backgroundColor: 'white', display: 'flex', flexDirection: 'column', rowGap: '2px' }}>
          <CRow style={{ height: '25px' }}>
            <CCol><p style={{ margin: 0, fontSize: '0.78rem' }}>Unable Deliver</p></CCol>
            <CCol><p style={{ margin: 0, fontSize: '0.78rem' }}>FWD Address</p></CCol>
            <CCol><p style={{ margin: 0, fontSize: '0.78rem' }}>Non-US</p></CCol>
          </CRow>
          <CRow style={{ height: '25px' }}>
            <CCol><TextField placeholder="0" value={getStatusValue(unableToDeliver, selectedData?.undeliverable)} size="small" fullWidth disabled={!isEditable} sx={fieldSx} /></CCol>
            <CCol><TextField placeholder="0" value={getStatusValue(forwardingAddress, selectedData?.forwardingAddress)} size="small" fullWidth disabled={!isEditable} sx={fieldSx} /></CCol>
            <CCol><TextField placeholder="0" value={getStatusValue(nonUS, selectedData?.nonUS)} size="small" fullWidth disabled={!isEditable} sx={fieldSx} /></CCol>
          </CRow>
        </CCardBody>
      </CCard>

      {/* P.O. Box / Invalid State */}
      <CCard style={{ margin: '20px 0 0', minHeight: '100px', border: 'none', boxShadow: 'none', borderBottom: '1px solid #ccc' }}>
        <CCardBody style={{ padding: '0.25rem 0.5rem', backgroundColor: 'white', display: 'flex', flexDirection: 'column', rowGap: '2px' }}>
          <CRow style={{ height: '25px' }}>
            <CCol><p style={{ margin: 0, fontSize: '0.78rem' }}>P.O. Box</p></CCol>
            <CCol><p style={{ margin: 0, fontSize: '0.78rem' }}>Invalid State</p></CCol>
            <CCol></CCol>
          </CRow>
          <CRow style={{ height: '25px' }}>
            <CCol><TextField placeholder="0" value={getStatusValue(isPOBox, selectedData?.poBox)} size="small" fullWidth disabled={!isEditable} sx={fieldSx} /></CCol>
            <CCol><TextField placeholder="0" value={getStatusValue(invalidState, selectedData?.badState)} size="small" fullWidth disabled={!isEditable} sx={fieldSx} /></CCol>
            <CCol></CCol>
          </CRow>
        </CCardBody>
      </CCard>
    </>
  );
};

export default PreviewReMailOptions;
