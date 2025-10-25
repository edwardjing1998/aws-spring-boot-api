import React, { useEffect, useMemo, useRef, useState } from 'react';
import {
  CCard, CCardBody, CCol, CRow, CButton, CFormSelect, CFormCheck,
} from '@coreui/react';

const EditFileReceivedFrom = ({ selectedData, setSelectedData, isEditable }) => {
  // helper: normalize any vendor shape to { vendId, vendName, queueForMail }
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
    // Try a few common shapes/legacy codes for queueForMail
    queueForMail: Boolean(
      v.queueForMail ??
      v.queForMail ??
      (typeof v.queForMailCd === 'string' ? v.queForMailCd.toUpperCase() === 'Y' : undefined) ??
      v.que_for_mail ??
      false
    ),
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
    setMoveAvailableFileReceivedFrom(prev => {
      const next = [...prev, ...toMove];
      // keep parent in sync
      setSelectedData?.(p => ({ ...p, vendorReceivedFrom: next }));
      return next;
    });
    setSelectedAvailIds([]);
  };

  const handleRemove = () => {
    const toMoveBack = moveAvailableFileReceivedFrom.filter(v => selectedSentIds.includes(v.vendId));
    const remainingRight = moveAvailableFileReceivedFrom.filter(v => !selectedSentIds.includes(v.vendId));
    setMoveAvailableFileReceivedFrom(remainingRight);
    setAvailableFileReceivedFrom(prev => [...prev, ...toMoveBack]);
    setSelectedSentIds([]);
    // keep parent in sync
    setSelectedData?.(p => ({ ...p, vendorReceivedFrom: remainingRight }));
  };

  // === Checkbox logic (queueForMail) ===
  const selectedRightVendors = useMemo(
    () => moveAvailableFileReceivedFrom.filter(v => selectedSentIds.includes(v.vendId)),
    [moveAvailableFileReceivedFrom, selectedSentIds]
  );

  const allTrue = selectedRightVendors.length > 0 && selectedRightVendors.every(v => v.queueForMail === true);
  const allFalse = selectedRightVendors.length > 0 && selectedRightVendors.every(v => v.queueForMail === false);
  const isIndeterminate = selectedRightVendors.length > 1 && !allTrue && !allFalse;

  const checkboxRef = useRef(null);
  useEffect(() => {
    if (checkboxRef.current) {
      checkboxRef.current.indeterminate = isIndeterminate;
    }
  }, [isIndeterminate, selectedRightVendors.length]);

  const currentChecked = selectedRightVendors.length === 0 ? false : allTrue; // fallback false when none

  const handleToggleQueueForMail = (e) => {
    const nextChecked = e.target.checked;
    if (selectedRightVendors.length === 0) return;

    const nextRight = moveAvailableFileReceivedFrom.map(v =>
      selectedSentIds.includes(v.vendId) ? { ...v, queueForMail: nextChecked } : v
    );
    setMoveAvailableFileReceivedFrom(nextRight);
    setSelectedData?.(p => ({ ...p, vendorReceivedFrom: nextRight }));
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

              {/* MIDDLE: Buttons + Queue checkbox */}
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

                {/* Queue for mail checkbox (applies to selection on the RIGHT) */}
                <div style={{ display: 'flex', alignItems: 'center', gap: 8 }}>
                  <CFormCheck
                    id="queueForMail"
                    label="Queue for mail"
                    checked={currentChecked}
                    onChange={handleToggleQueueForMail}
                    disabled={!isEditable || selectedRightVendors.length === 0}
                    // get the underlying <input> to set indeterminate
                    inputRef={checkboxRef}
                  />
                </div>

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
                      {/* Show a small marker for queue state */}
                      {vendor.vendId} — {vendor.vendName} {vendor.queueForMail ? ' (Q)' : ''}
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
