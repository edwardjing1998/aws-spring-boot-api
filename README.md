import React from 'react';
import { CFormTextarea, CCard, CCardBody } from '@coreui/react';

// --- Interfaces ---

export interface SysPrinNotesData {
  notes?: string;
  [key: string]: any;
}

interface PreviewSysPrinNotesProps {
  data?: SysPrinNotesData;
}

const PreviewSysPrinNotes: React.FC<PreviewSysPrinNotesProps> = ({ data }) => {
  return (
    <>
      <CCard
        style={{
          height: '35px',
          marginBottom: '4px',
          marginTop: '2px',
          border: 'none',             // remove border
          backgroundColor: '#f3f6f8', // light light green
          boxShadow: 'none',          // remove any shadow
          borderRadius: '4px'         // optional: rounded corners
        }}
      >
        <CCardBody
          className="d-flex align-items-center"
          style={{
            padding: '0.25rem 0.5rem',
            height: '100%',
            backgroundColor: 'transparent' // inherit from parent
          }}
        >
          <p
            style={{
              margin: 0,
              fontSize: '0.78rem',
              fontWeight: '500'
            }}
          >
            General
          </p>
        </CCardBody>
      </CCard>
      
      <CCard style={{ height: '150px', marginBottom: '4px', marginTop: '12px', border: '0px solid gray' }}>
        <CCardBody
          className="d-flex align-items-center"
          style={{ padding: '0.25rem 0.5rem', height: '100%' }}
        >
          <div style={{ width: '100%', height: '100%' }}>
            <CFormTextarea
              id="special-notes"
              aria-label="Special Processing Notes"
              value={data?.notes ?? ''}
              readOnly
              style={{
                height: '145px',
                fontSize: '0.78rem',
                fontFamily: 'Segoe UI, sans-serif',
                fontWeight: '500',
                overflowY: 'auto',
                resize: 'vertical',
              }}
            />
          </div>
        </CCardBody>
      </CCard>
    </>
  );
};

export default PreviewSysPrinNotes;
