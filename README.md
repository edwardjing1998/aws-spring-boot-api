import React, { useState } from 'react';
import PreviewSubmitInformationA from './components/PreviewSubmitInformationA';
import PreviewSubmitInformationB from './components/PreviewSubmitInformationB';
import PreviewSysPrinNotes from './components/PreviewSysPrinNotes';

// ---- Page 2 ----
const getPageButtonStyle = (isActive) => ({
  marginRight: 8,
  padding: '3px 10px',
  fontSize: '0.78rem',          // smaller font size
  borderRadius: 12,
  border: '1px solid #8ecae6',  // light blue border
  backgroundColor: isActive ? '#BEEBE2' : '#ffffff', // active / inactive
  color: '#000',
  cursor: isActive ? 'default' : 'pointer',
  minWidth: 32,
});

// ---- Parent with pagination for 2 pages ----
const PreviewSubmitInformation = ({ selectedData, isEditable, sharedSx, getStatusValue }) => {
  const [page, setPage] = useState(1);   // 1 or 2

  const handleSubmit = () => {
    // here you call your REST API to submit to DB
    console.log('Submitting payload:', selectedData);
    alert('Submitted! Check console for payload.');
  };

  return (
    <div style={{ border: '1px solid #ddd', borderRadius: 8, padding: 16, width: '100%' }}>
      {/* Simple "pagination" header */}
      <div style={{ display: 'flex', justifyContent: 'center', marginBottom: 12 }}>
        <button
            onClick={() => setPage(1)}
            disabled={page === 1}
            style={getPageButtonStyle(page === 1)}
        >
            1
        </button>
        <button
            onClick={() => setPage(2)}
            disabled={page === 2}
            style={getPageButtonStyle(page === 2)}
        >
            2
        </button>
        <button
            onClick={() => setPage(3)}
            disabled={page === 3}
            style={getPageButtonStyle(page === 3)}
        >
            3
        </button>
      </div>

      {/* Render current page */}
      {page === 1 && (
        <PreviewSubmitInformationA  
            selectedData={selectedData}
            isEditable={isEditable}
            sharedSx={sharedSx}
            getStatusValue={getStatusValue} />
      )}
      {page === 2 && (
        <PreviewSubmitInformationB
        selectedData={selectedData}
        sharedSx={sharedSx} />
      )}
      {page === 3 && (
        <PreviewSysPrinNotes data={selectedData || []} />
      )}

      {/* Footer buttons */}
      <div
        style={{
          marginTop: 16,
          display: 'flex',
          justifyContent: 'space-between',
          alignItems: 'center',
        }}
      >
      </div>
    </div>
  );
};

export default PreviewSubmitInformation;
