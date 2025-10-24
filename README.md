import React, { useEffect, useState } from 'react';
import {
  CCard,
  CCardBody,
  CCol,
  CRow,
  CButton,
  CFormSelect,
} from '@coreui/react';

const EditFileReceivedFrom = ({ selectedData, setSelectedData, isEditable }) => {
  const [availableFileReceivedFrom, setAvailableFileReceivedFrom] = useState([]);
  const [moveAvailableFileReceivedFrom, setMoveAvailableFileReceivedFrom] = useState(
    (selectedData?.vendorReceivedFrom ?? []).map(v => ({
      vendId: v.vendorId,
      vendName: v.vendorName,
    }))
  );

  const [selectedAvailIds, setSelectedAvailIds] = useState([]);
  const [selectedSentIds, setSelectedSentIds] = useState([]);

  useEffect(() => {
   // fetch('http://localhost:4444/api/vendors?fileIo=O')
      fetch('http://localhost:8089/client-sysprin-reader/api/vendor?fileIo=O')
      .then((r) => r.json())
      .then((data) => setAvailableFileReceivedFrom(data))
      .catch((err) => console.error('Failed to load vendors', err));
  }, []);

  const handleAdd = () => {
    const toMove = availableFileReceivedFrom.filter(v => selectedAvailIds.includes(v.vendId));
    setAvailableFileReceivedFrom(availableFileReceivedFrom.filter(v => !selectedAvailIds.includes(v.vendId)));
    setMoveAvailableFileReceivedFrom([...moveAvailableFileReceivedFrom, ...toMove]);
    setSelectedAvailIds([]);
  };

  const handleRemove = () => {
    const toMove = moveAvailableFileReceivedFrom.filter(v => selectedSentIds.includes(v.vendId));
    setMoveAvailableFileReceivedFrom(moveAvailableFileReceivedFrom.filter(v => !selectedSentIds.includes(v.vendId)));
    setAvailableFileReceivedFrom([...availableFileReceivedFrom, ...toMove]);
    setSelectedSentIds([]);
  };

  const selectStyle = {
    height: '350px',
    fontSize: '0.78rem',
    width: '100%',
    maxWidth: '350px',
    paddingLeft: '16px',
    scrollbarWidth: 'none',         // Firefox
    msOverflowStyle: 'none',        // IE 10+
  };

  const optionStyle = {
    fontSize: '0.78rem',
    borderBottom: '1px dotted #ccc',
    padding: '4px 6px'
  };

  const buttonStyle = {
    width: '120px',
    fontSize: '0.78rem',
  };

  return (
    <CRow>
      <CCol xs={12}>
        <CCard className="mb-4">
          <CCardBody>
            <CRow className="align-items-center">
              <CCol md={5} className="order-md-1">
                <CFormSelect
                  multiple
                  size="10"
                  style={selectStyle}
                  value={selectedAvailIds}
                  onChange={(e) =>
                    setSelectedAvailIds([...e.target.selectedOptions].map(o => o.value))
                  }
                  disabled={!isEditable}
                >
                  {availableFileReceivedFrom.map(vendor => (
                    <option key={vendor.vendId} value={vendor.vendId} style={optionStyle}>
                      {vendor.vendName}
                    </option>
                  ))}
                </CFormSelect>
              </CCol>

              <CCol md={2} className="d-flex flex-column align-items-center justify-content-center gap-2 order-md-2" style={{ height: '100%' }}>
                <CButton
                  color="success"
                  variant="outline"
                  size="sm"
                  style={buttonStyle}
                  onClick={handleAdd}
                  disabled={!isEditable || selectedAvailIds.length === 0}
                >
                  Add ⬇️
                </CButton>
                <CButton
                  color="danger"
                  variant="outline"
                  size="sm"
                  style={buttonStyle}
                  onClick={handleRemove}
                  disabled={!isEditable || selectedSentIds.length === 0}
                >
                  ⬆️ Remove
                </CButton>
              </CCol>

              <CCol md={5} className="order-md-3 d-flex justify-content-end">          
                <CFormSelect
                  multiple
                  size="10"
                  style={selectStyle}
                  value={selectedSentIds}
                  onChange={(e) =>
                    setSelectedSentIds([...e.target.selectedOptions].map(o => o.value))
                  }
                  disabled={!isEditable}
                >
                  {moveAvailableFileReceivedFrom.map(vendor => (
                    <option key={vendor.vendId} value={vendor.vendId} style={optionStyle}>
                      {vendor.vendName}
                    </option>
                  ))}
                </CFormSelect>
              </CCol>
            </CRow>
          </CCardBody>
        </CCard>
      </CCol>
    </CRow>
  );
};

export default EditFileReceivedFrom;
