import React, { useState } from 'react';
import { CCard, CCardBody, CRow, CCol } from '@coreui/react';
import TextField from '@mui/material/TextField';
import { SxProps, Theme } from '@mui/material';

import {
  unableToDeliver,
  forwardingAddress,
  nonUS,
  invalidState,
  isPOBox,
} from '../../utils/ClientInfoFieldValueMapping';

interface OptionItem {
  code: string;
  label: string;
}

interface SysPrinData {
  holdDays?: number | string;
  tempAway?: number | string;
  tempAwayAtts?: number | string;
  undeliverable?: string;
  forwardingAddress?: string;
  nonUS?: string;
  poBox?: string;
  badState?: string;
  [key: string]: any;
}

interface PreviewReMailOptionsProps {
  selectedData?: SysPrinData;
  sharedSx?: SxProps<Theme>;
}

const PreviewReMailOptions: React.FC<PreviewReMailOptionsProps> = ({ selectedData, sharedSx }) => {
  // eslint-disable-next-line @typescript-eslint/no-unused-vars
  const [isEditable, setIsEditable] = useState(false);

  const getStatusValue = (options: OptionItem[], code: string | undefined | null): string => {
    if (!code) return '';
    const match = options.find((opt) => opt.code === String(code));
    return match ? match.label : '';
  };

  const fieldSx: SxProps<Theme> = {
    ...(sharedSx as any), // Cast to any to merge if sharedSx structure is complex, or refine type
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
