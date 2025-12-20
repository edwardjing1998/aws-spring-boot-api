import React, { useState } from 'react';
import { CCard, CCardBody, CRow, CCol } from '@coreui/react';
import { SxProps, Theme } from '@mui/material';

// Ensure these paths match your project structure
import PreviewSubmissionFilesSentTo from '../vendor/PreviewSubmissionFilesSentTo';
import PreviewSubmissionFilesReceivedFrom from '../vendor/PreviewSubmissionFilesReceivedFrom';

import {
  unableToDeliver,
  forwardingAddress,
  nonUS,
  invalidState,
  isPOBox,
} from '../../utils/ClientInfoFieldValueMapping';

// --- Interfaces ---

interface OptionItem {
  code: string;
  label: string;
}

interface PreviewSubmitInformationBProps {
  selectedData?: {
    holdDays?: string | number;
    tempAway?: string | number;
    tempAwayAtts?: string | number;
    poBox?: string;
    undeliverable?: string;
    forwardingAddress?: string;
    nonUS?: string;
    badState?: string;
    vendorSentTo?: any[];
    vendorReceivedFrom?: any[];
    [key: string]: any;
  };
  sharedSx?: SxProps<Theme>;
}

const PreviewSubmitInformationB: React.FC<PreviewSubmitInformationBProps> = ({ selectedData, sharedSx }) => {
  // eslint-disable-next-line @typescript-eslint/no-unused-vars
  const [isEditable, setIsEditable] = useState(false);

  const getStatusValue = (options: OptionItem[], code: string | number | undefined): string => {
    if (!code) return '';
    const match = options.find((opt) => opt.code === String(code));
    return match ? match.label : '';
  };

  // Note: fieldSx was defined in original JS but unused in JSX (values rendered in <p> tags). 
  // Kept here for reference or future use.
  // eslint-disable-next-line @typescript-eslint/no-unused-vars
  const fieldSx: SxProps<Theme> = {
    ...(sharedSx as any),
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
      <CCard style={{ height: '25px', marginBottom: '4px', marginTop: '2px', border: 'none', backgroundColor: '#f3f6f8', boxShadow: 'none', borderRadius: '4px' }}>
        <CCardBody className="d-flex align-items-center" style={{ padding: '0.25rem 0.5rem', height: '100%', backgroundColor: 'transparent' }}>
          <p style={{ margin: 0, fontSize: '0.78rem', fontWeight: '500' }}>Remail Options</p>
        </CCardBody>
      </CCard>

      {/* Hold Days / Temp Aways / Attempts */}
      <CCard style={{ margin: '10px 0 0', minHeight: '20px', border: 'none', boxShadow: 'none', borderBottom: '1px solid #ccc' }}>
        <CCardBody style={{ padding: '0.25rem 0.5rem', backgroundColor: 'white', display: 'flex', flexDirection: 'column', rowGap: '4px' }}>
          <CRow style={{ height: '20px' }}>
            <CCol><p style={{ margin: 0, fontSize: '0.78rem' }}>Hold Days</p></CCol>
            <CCol><p style={{ margin: 0, fontSize: '0.78rem' }}>Temp Aways</p></CCol>
            <CCol><p style={{ margin: 0, fontSize: '0.78rem' }}>Attempts</p></CCol>
            <CCol><p style={{ margin: 0, fontSize: '0.78rem' }}>P.O. Box</p></CCol>
          </CRow>
          <CRow style={{ height: '20px' }}>
            <CCol><p style={{ margin: 0, fontSize: '0.78rem' }}> {selectedData?.holdDays ?? ''}</p></CCol>
            <CCol><p style={{ margin: 0, fontSize: '0.78rem' }}> {selectedData?.tempAway ?? ''}</p></CCol>
            <CCol><p style={{ margin: 0, fontSize: '0.78rem' }}> {selectedData?.tempAwayAtts ?? ''}</p></CCol>
            <CCol><p style={{ margin: 0, fontSize: '0.78rem' }}> {getStatusValue(isPOBox, selectedData?.poBox)}</p></CCol>
          </CRow>
        </CCardBody>
      </CCard>

      {/* Unable Deliver / FWD Address / Non-US */}
      <CCard style={{ margin: '10px 0 0', minHeight: '20px', border: 'none', boxShadow: 'none', borderBottom: '1px solid #ccc' }}>
        <CCardBody style={{ padding: '0.25rem 0.5rem', backgroundColor: 'white', display: 'flex', flexDirection: 'column', rowGap: '2px' }}>
          <CRow style={{ height: '20px' }}>
            <CCol><p style={{ margin: 0, fontSize: '0.78rem' }}>Unable Deliver</p></CCol>
            <CCol><p style={{ margin: 0, fontSize: '0.78rem' }}>FWD Address</p></CCol>
            <CCol><p style={{ margin: 0, fontSize: '0.78rem' }}>Non-US</p></CCol>
            <CCol><p style={{ margin: 0, fontSize: '0.78rem' }}>Invalid State</p></CCol>
          </CRow>
          <CRow style={{ height: '20px' }}>
            <CCol><p style={{ margin: 0, fontSize: '0.78rem' }}> {getStatusValue(unableToDeliver, selectedData?.undeliverable)}</p></CCol>
            <CCol><p style={{ margin: 0, fontSize: '0.78rem' }}> {getStatusValue(forwardingAddress, selectedData?.forwardingAddress)}</p></CCol>
            <CCol><p style={{ margin: 0, fontSize: '0.78rem' }}> {getStatusValue(nonUS, selectedData?.nonUS)}</p></CCol>
            <CCol><p style={{ margin: 0, fontSize: '0.78rem' }}> {getStatusValue(invalidState, selectedData?.badState)}</p></CCol>
          </CRow>
        </CCardBody>
      </CCard>

      {/* P.O. Box / Invalid State */}
      <CCard
        style={{
          margin: '10px 0 0',
          minHeight: '20px',
          border: 'none',
          boxShadow: 'none',
          borderBottom: '1px solid #ccc',
          width: '100%',          // 👈 make CCard full width
        }}
      >
        <CCardBody
          style={{
            padding: '0.25rem 0.5rem',
            backgroundColor: 'white',
            display: 'flex',
            flexDirection: 'column',
            rowGap: '2px',
          }}
        >
          <CRow style={{ height: '200px' }}>
            <CCol xs={6}>
                <PreviewSubmissionFilesSentTo data={selectedData?.vendorSentTo || []} />
            </CCol>
            <CCol xs={6}>
                <PreviewSubmissionFilesReceivedFrom data={selectedData?.vendorReceivedFrom || []} />
            </CCol>
          </CRow>
        </CCardBody>
      </CCard>
    </>
  );
};

export default PreviewSubmitInformationB;
