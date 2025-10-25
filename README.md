import React, { useEffect, useState } from 'react';
import {
  CCard, CCardBody, CCol, CRow, CButton, CFormSelect,
} from '@coreui/react';

const EditFileReceivedFrom = ({ selectedData, setSelectedData, isEditable }) => {
  // helper: normalize any vendor shape to { vendId, vendName }
  const normalizeVendor = (v = {}) => ({
    vendId: String(
      v.vendId ??
      v.vendorId ??
      v.vendor?.id ??
      v.vendor?.vendId ??
      ''
    ),
    vendName:
      v.vendName ??
      v.vendorName ??
      v.vendor?.name ??
      v.vendor?.vendNm ??
      String(v.vendId ?? v.vendorId ?? ''), // fallback to id
  });

  const [availableFileReceivedFrom, setAvailableFileReceivedFrom] = useState([]);
  const [moveAvailableFileReceivedFrom, setMoveAvailableFileReceivedFrom] = useState([]);
  const [selectedAvailIds, setSelectedAvailIds] = useState([]);
  const [selectedSentIds, setSelectedSentIds] = useState([]);

  // 1) Seed RIGHT list from selectedData whenever it changes
  useEffect(() => {
    const seeded = (selectedData?.vendorReceivedFrom ?? [])
      .map(normalizeVendor)
      .filter(v => v.vendId); // drop empties
    setMoveAvailableFileReceivedFrom(seeded);
  }, [selectedData?.vendorReceivedFrom]);

  // 2) Load LEFT list; exclude anything already in RIGHT
  useEffect(() => {
    fetch('http://localhost:8089/client-sysprin-reader/api/vendor?fileIo=O')
      .then((r) => r.json())
      .then((data) => {
        const all = (Array.isArray(data) ? data : []).map(normalizeVendor);
        const rightIds = new Set(moveAvailableFileReceivedFrom.map(v => v.vendId));
        const left = all.filter(v => !rightIds.has(v.vendId));
        setAvailableFileReceivedFrom(left);
      })
      .catch((err) => console.error('Failed to load vendors', err));
    // re-run when right side changes so the left list stays de-duplicated
  }, [moveAvailableFileReceivedFrom]);

  const handleAdd = () => {
    const toMove = availableFileReceivedFrom.filter(v => selectedAvailIds.includes(v.vendId));
    const remaining = availableFileReceivedFrom.filter(v => !selectedAvailIds.includes(v.vendId));
    setAvailableFileReceivedFrom(remaining);
    setMoveAvailableFileReceivedFrom(prev => [...prev, ...toMove]);
    setSelectedAvailIds([]);
  };

  const handleRemove = () => {
    const toMoveBack = moveAvailableFileReceivedFrom.filter(v => selectedSentIds.includes(v.vendId));
    const remainingRight = moveAvailableFileReceivedFrom.filter(v => !selectedSentIds.includes(v.vendId));
    setMoveAvailableFileReceivedFrom(remainingRight);
    setAvailableFileReceivedFrom(prev => [...prev, ...toMoveBack]);
    setSelectedSentIds([]);
  };

  const selectStyle = {
    height: '350px',
    fontSize: '0.78rem',
    width: '100%',
    maxWidth: '350px',
    paddingLeft: '16px',
    scrollbarWidth: 'none', // Firefox
    msOverflowStyle: 'none', // IE 10+
  };

  const optionStyle = {
    fontSize: '0.78rem',
    borderBottom: '1px dotted #ccc',
    padding: '4px 6px',
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
              {/* LEFT: Available vendors */}
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
                      {vendor.vendId} — {vendor.vendName}
                    </option>
                  ))}
                </CFormSelect>
              </CCol>

              {/* MIDDLE: Buttons (increased vertical gap via inline gap: '24px') */}
              <CCol
                md={2}
                className="d-flex flex-column align-items-center justify-content-center order-md-2"
                style={{ minHeight: '200px', gap: '24px' }}
              >
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

              {/* RIGHT: Selected vendors */}
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
                      {vendor.vendId} — {vendor.vendName}
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
