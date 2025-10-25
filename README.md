import React, { useEffect, useState, useMemo } from 'react';
import {
  CCard, CCardBody, CCol, CRow, CButton, CFormSelect,
} from '@coreui/react';

const EditFileSentTo = ({ selectedData, setSelectedData, isEditable }) => {
  const [availableSentFileTo, setAvailableSentFileTo] = useState([]);
  const [moveAvailableSentFileTo, setMoveAvailableSentFileto] = useState([]);
  const [selectedAvailIds, setSelectedAvailIds] = useState([]);
  const [selectedSentIds, setSelectedSentIds] = useState([]);

  // --- 1) Helper: normalize various possible shapes into { vendId, vendName }
  const normalizeVendor = (v = {}) => ({
    vendId: String(
      v.vendId ?? v.vendorId ?? v.vendor?.id ?? v.vendor?.vendId ?? ''
    ),
    vendName:
      v.vendName ??
      v.vendorName ??
      v.vendor?.name ??
      v.vendor?.vendNm ??
      String(v.vendId ?? v.vendorId ?? ''),
  });

  // --- 2) Seed RIGHT list from selectedData.vendorSentTo whenever selectedData changes
  useEffect(() => {
    const right = (selectedData?.vendorSentTo ?? [])
      .map(normalizeVendor)
      .filter(v => v.vendId);
    setMoveAvailableSentFileto(right);
    // Optional: clear selections when sysPrin changes
    setSelectedSentIds([]);
  }, [selectedData?.vendorSentTo]);

  // --- 3) Load LEFT list (available vendors) and normalize
  useEffect(() => {
    fetch('http://localhost:8089/client-sysprin-reader/api/vendor?fileIo=I')
      .then((r) => r.json())
      .then((data) => {
        const left = (Array.isArray(data) ? data : []).map(normalizeVendor);
        setAvailableSentFileTo(left);
      })
      .catch((err) => console.error('Failed to load vendors', err));
  }, []);

  // --- 4) Derived left list that excludes anything already on the right
  const leftFiltered = useMemo(() => {
    const rightIds = new Set(moveAvailableSentFileTo.map(v => v.vendId));
    return availableSentFileTo.filter(v => !rightIds.has(v.vendId));
  }, [availableSentFileTo, moveAvailableSentFileTo]);

  const handleAdd = () => {
    const toMove = leftFiltered.filter(v => selectedAvailIds.includes(v.vendId));
    // add to right
    setMoveAvailableSentFileto(prev => {
      const ids = new Set(prev.map(p => p.vendId));
      const merged = [...prev, ...toMove.filter(t => !ids.has(t.vendId))];
      return merged;
    });
    // clear selection in left
    setSelectedAvailIds([]);
  };

  const handleRemove = () => {
    const toMoveBack = moveAvailableSentFileTo.filter(v => selectedSentIds.includes(v.vendId));
    // remove from right
    setMoveAvailableSentFileto(moveAvailableSentFileTo.filter(v => !selectedSentIds.includes(v.vendId)));
    // return to left pool (available)
    setAvailableSentFileTo(prev => {
      const ids = new Set(prev.map(p => p.vendId));
      const merged = [...prev, ...toMoveBack.filter(t => !ids.has(t.vendId))];
      return merged;
    });
    // clear selection in right
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

  const buttonStyle = { width: '120px', fontSize: '0.78rem' };

  return (
    <CRow>
      <CCol xs={12}>
        <CCard className="mb-4">
          <CCardBody>
            <CRow className="align-items-center">
              {/* LEFT: available (filtered to exclude items already on right) */}
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
                  {leftFiltered.map(vendor => (
                    <option key={vendor.vendId} value={vendor.vendId} style={optionStyle}>
                      {vendor.vendId} — {vendor.vendName}
                    </option>
                  ))}
                </CFormSelect>
              </CCol>

              {/* MIDDLE buttons */}
              <CCol
                md={2}
                className="d-flex flex-column align-items-center justify-content-center gap-2 order-md-2"
                style={{ minHeight: '200px' }}
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

              {/* RIGHT: already selected (seeded from selectedData on mount/change) */}
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
                  {moveAvailableSentFileTo.map(vendor => (
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

export default EditFileSentTo;





curl -X 'POST' \
  'http://localhost:8089/client-sysprin-writer/api/sysprins/54510019/sent-to/create' \
  -H 'accept: */*' \
  -H 'Content-Type: application/json' \
  -d '{
  "sysPrin": "54510019",
  "vendorId": "v04",
  "queForMail": true
}'



curl -X 'DELETE' \
  'http://localhost:8089/client-sysprin-writer/api/sysprins/54510019/sent-to/delete' \
  -H 'accept: */*' \
  -H 'Content-Type: application/json' \
  -d '{
  "sysPrin": "54510019",
  "vendorId": "v04",
  "queForMail": true
}'
