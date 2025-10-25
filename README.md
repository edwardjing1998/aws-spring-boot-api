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

  // track which side was interacted with last: 'left' | 'right'
  const [activeSide, setActiveSide] = useState('left');

  // Busy states for API calls
  const [adding, setAdding] = useState(false);
  const [removing, setRemoving] = useState(false);

  // 1) Seed RIGHT list from selectedData whenever it changes
  useEffect(() => {
    const seeded = (selectedData?.vendorReceivedFrom ?? [])
      .map(normalizeVendor)
      .filter(v => v.vendId);
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
  }, [moveAvailableFileReceivedFrom]);

  // === API: add a single "received-from" record ===
  async function postAddReceivedFrom(sysPrin, vendorId, queForMail) {
    const url = `http://localhost:8089/client-sysprin-writer/api/sysprins/${encodeURIComponent(sysPrin)}/received-from/create`;
    const res = await fetch(url, {
      method: 'POST',
      headers: { accept: '*/*', 'Content-Type': 'application/json' },
      body: JSON.stringify({ sysPrin, vendorId, queForMail }),
    });
    if (!res.ok) {
      let msg = `Request failed (${res.status})`;
      try {
        const ct = res.headers.get('Content-Type') || '';
        if (ct.includes('application/json')) {
          const j = await res.json();
          msg = j?.message || JSON.stringify(j);
        } else {
          msg = await res.text();
        }
      } catch {}
      throw new Error(msg);
    }
    return true;
  }

  // === API: delete a single "received-from" record ===
  async function deleteReceivedFrom(sysPrin, vendorId, queForMail) {
    const url = `http://localhost:8089/client-sysprin-writer/api/sysprins/${encodeURIComponent(sysPrin)}/received-from/delete`;
    const res = await fetch(url, {
      method: 'DELETE',
      headers: { accept: '*/*', 'Content-Type': 'application/json' },
      body: JSON.stringify({ sysPrin, vendorId, queForMail }),
    });
    if (!res.ok) {
      let msg = `Request failed (${res.status})`;
      try {
        const ct = res.headers.get('Content-Type') || '';
        if (ct.includes('application/json')) {
          const j = await res.json();
          msg = j?.message || JSON.stringify(j);
        } else {
          msg = await res.text();
        }
      } catch {}
      throw new Error(msg);
    }
    return true;
  }

  // === Checkbox logic (queueForMail) — applies to LEFT selection ===
  const selectedLeftVendors = useMemo(
    () => availableFileReceivedFrom.filter(v => selectedAvailIds.includes(v.vendId)),
    [availableFileReceivedFrom, selectedAvailIds]
  );

  const allTrue = selectedLeftVendors.length > 0 && selectedLeftVendors.every(v => v.queueForMail === true);
  const allFalse = selectedLeftVendors.length > 0 && selectedLeftVendors.every(v => v.queueForMail === false);
  const isIndeterminate = selectedLeftVendors.length > 1 && !allTrue && !allFalse;

  const checkboxRef = useRef(null);
  useEffect(() => {
    if (checkboxRef.current) {
      checkboxRef.current.indeterminate = isIndeterminate;
    }
  }, [isIndeterminate, selectedLeftVendors.length]);

  const currentChecked = selectedLeftVendors.length === 0 ? false : allTrue; // fallback false when none

  const handleToggleQueueForMail = (e) => {
    const nextChecked = e.target.checked;
    if (selectedLeftVendors.length === 0) return;
    const nextLeft = availableFileReceivedFrom.map(v =>
      selectedAvailIds.includes(v.vendId) ? { ...v, queueForMail: nextChecked } : v
    );
    setAvailableFileReceivedFrom(nextLeft);
  };

  const selectStyle = {
    height: '350px',
    fontSize: '0.78rem',
    width: '100%',
    maxWidth: '350px',
    paddingLeft: '16px',
    scrollbarWidth: 'none',
    msOverflowStyle: 'none',
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

  // Enable checkbox when left is active, disable when right is active.
  const checkboxDisabled =
    !isEditable ||
    adding ||
    removing ||
    activeSide === 'right' ||
    (activeSide === 'left' && selectedAvailIds.length === 0);

  // === ADD: call API then move successful items to RIGHT ===
  const handleAdd = async () => {
    if (!isEditable || selectedAvailIds.length === 0) return;

    const sysPrin = selectedData?.sysPrin || '';
    if (!sysPrin) {
      alert('Missing sysPrin on the current record.');
      return;
    }

    setAdding(true);
    try {
      const selectedLeft = availableFileReceivedFrom.filter(v => selectedAvailIds.includes(v.vendId));

      const results = await Promise.allSettled(
        selectedLeft.map(v =>
          postAddReceivedFrom(sysPrin, v.vendId, v.queueForMail)
            .then(() => ({ ok: true, vendId: v.vendId, vendor: v }))
            .catch((err) => ({ ok: false, vendId: v.vendId, error: err?.message || 'Failed', vendor: v }))
        )
      );

      const successes = results
        .filter(r => (r.status === 'fulfilled' ? r.value.ok : r.value?.ok))
        .map(r => (r.status === 'fulfilled' ? r.value.vendor : r.value.vendor));

      const failures  = results.filter(r => !(r.status === 'fulfilled' ? r.value.ok : r.value?.ok));

      if (successes.length > 0) {
        const successIds = new Set(successes.map(v => v.vendId));
        const remainingLeft = availableFileReceivedFrom.filter(v => !successIds.has(v.vendId));
        setAvailableFileReceivedFrom(remainingLeft);

        setMoveAvailableFileReceivedFrom(prev => {
          const next = [...prev, ...successes];
          setSelectedData?.(p => ({ ...p, vendorReceivedFrom: next }));
          return next;
        });
      }

      if (failures.length > 0) {
        const msg = failures
          .map(f => {
            const v = f.value?.vendor ?? f.reason?.vendor;
            const err = f.value?.error ?? f.reason?.error ?? 'Failed';
            const id = v?.vendId || '?';
            return `${id}: ${err}`;
          })
          .join('\n');
        alert(`Some vendors could not be added:\n${msg}`);
      }

      setSelectedAvailIds([]);
    } finally {
      setAdding(false);
    }
  };

  // === REMOVE: call DELETE API then move successful items to LEFT ===
  const handleRemove = async () => {
    if (!isEditable || selectedSentIds.length === 0) return;

    const sysPrin = selectedData?.sysPrin || '';
    if (!sysPrin) {
      alert('Missing sysPrin on the current record.');
      return;
    }

    setRemoving(true);
    try {
      const selectedRight = moveAvailableFileReceivedFrom.filter(v => selectedSentIds.includes(v.vendId));

      const results = await Promise.allSettled(
        selectedRight.map(v =>
          deleteReceivedFrom(sysPrin, v.vendId, v.queueForMail)
            .then(() => ({ ok: true, vendId: v.vendId, vendor: v }))
            .catch((err) => ({ ok: false, vendId: v.vendId, error: err?.message || 'Failed', vendor: v }))
        )
      );

      const successes = results
        .filter(r => (r.status === 'fulfilled' ? r.value.ok : r.value?.ok))
        .map(r => (r.status === 'fulfilled' ? r.value.vendor : r.value.vendor));

      const failures  = results.filter(r => !(r.status === 'fulfilled' ? r.value.ok : r.value?.ok));

      if (successes.length > 0) {
        const successIds = new Set(successes.map(v => v.vendId));
        const remainingRight = moveAvailableFileReceivedFrom.filter(v => !successIds.has(v.vendId));
        setMoveAvailableFileReceivedFrom(remainingRight);

        setAvailableFileReceivedFrom(prev => [...prev, ...successes]);

        // keep parent in sync
        setSelectedData?.(p => ({ ...p, vendorReceivedFrom: remainingRight }));
      }

      if (failures.length > 0) {
        const msg = failures
          .map(f => {
            const v = f.value?.vendor ?? f.reason?.vendor;
            const err = f.value?.error ?? f.reason?.error ?? 'Failed';
            const id = v?.vendId || '?';
            return `${id}: ${err}`;
          })
          .join('\n');
        alert(`Some vendors could not be removed:\n${msg}`);
      }

      setSelectedSentIds([]);
    } finally {
      setRemoving(false);
    }
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
                  onChange={(e) => {
                    setSelectedAvailIds([...e.target.selectedOptions].map(o => o.value));
                    setActiveSide('left');
                  }}
                  onClick={() => setActiveSide('left')}
                  disabled={!isEditable || adding || removing}
                >
                  {availableFileReceivedFrom.map(vendor => (
                    <option key={vendor.vendId} value={vendor.vendId} style={optionStyle}>
                      {vendor.vendId} — {vendor.vendName} {vendor.queueForMail ? ' (Q)' : ''}
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
                  disabled={!isEditable || selectedAvailIds.length === 0 || adding || removing}
                >
                  {adding ? 'Adding…' : 'Add ⬇️'}
                </CButton>

                {/* Queue for mail checkbox (applies to selection on the LEFT) */}
                <div
                  style={{
                    display: 'flex',
                    alignItems: 'center',
                    gap: 8,
                    fontSize: '0.72rem',
                    lineHeight: 1.1,
                  }}
                >
                  <CFormCheck
                    id="queueForMail"
                    checked={currentChecked}
                    onChange={handleToggleQueueForMail}
                    disabled={checkboxDisabled}
                    inputRef={checkboxRef}
                    label=""
                  />
                  <label
                    htmlFor="queueForMail"
                    style={{ margin: 0, cursor: checkboxDisabled ? 'default' : 'pointer' }}
                  >
                    Queue for mail
                  </label>
                </div>

                <CButton
                  color="danger"
                  variant="outline"
                  size="sm"
                  style={buttonStyle}
                  onClick={handleRemove}
                  disabled={!isEditable || selectedSentIds.length === 0 || adding || removing}
                >
                  {removing ? 'Removing…' : '⬆️ Remove'}
                </CButton>
              </CCol>

              {/* RIGHT: Selected vendors */}
              <CCol md={5} className="order-md-3 d-flex justify-content-end">
                <CFormSelect
                  multiple
                  size="10"
                  style={selectStyle}
                  value={selectedSentIds}
                  onChange={(e) => {
                    setSelectedSentIds([...e.target.selectedOptions].map(o => o.value));
                    setActiveSide('right');
                  }}
                  onClick={() => setActiveSide('right')}
                  disabled={!isEditable || adding || removing}
                >
                  {moveAvailableFileReceivedFrom.map(vendor => (
                    <option key={vendor.vendId} value={vendor.vendId} style={optionStyle}>
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
