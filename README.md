import React, { useEffect, useMemo, useRef, useState } from 'react';
import {
  CCard, CCardBody, CCol, CRow, CButton, CFormSelect, CFormCheck,
} from '@coreui/react';

const EditFileReceivedFrom = ({ selectedData, setSelectedData, isEditable }) => {
  // ---- helpers --------------------------------------------------------------

  // Normalize anything into our local UI shape
  const normalizeVendor = (v = {}) => ({
    vendId: String(
      v?.vendId ??
      v?.vendorId ??
      v?.vendor?.id ??
      v?.vendor?.vendId ??
      ''
    ),
    vendName:
      v?.vendName ??
      v?.vendorName ??
      v?.vendor?.name ??
      v?.vendor?.vendNm ??
      String(v?.vendId ?? v?.vendorId ?? ''),
    // accept 1/'1'/true/'Y'/'TRUE'
    queueForMail: (() => {
      const raw =
        v?.queueForMail ??
        v?.queForMail ??
        v?.que_for_mail ??
        v?.queForMailCd ??
        v?.queue_for_mail_cd;
      if (typeof raw === 'string') {
        const s = raw.trim().toUpperCase();
        return s === '1' || s === 'Y' || s === 'TRUE';
      }
      if (typeof raw === 'number') return raw === 1;
      return Boolean(raw);
    })(),
  });

  // Convert local UI shape -> parent shape (rich & tolerant)
  const toParentShape = (v) => {
    const sysPrin = selectedData?.sysPrin ?? '';
    return {
      vendorId: v.vendId,
      vendId: v.vendId,
      vendName: v.vendName,
      queueForMail: v.queueForMail,
      queForMail: v.queueForMail,
      queForMailCd: v.queueForMail ? 'Y' : 'N',
      ...(sysPrin ? { id: { sysPrin, vendorId: v.vendId } } : {}),
      vendor: { vendId: v.vendId, vendNm: v.vendName },
    };
  };

  // Write RIGHT list back to parent
  const syncRightToParent = (rightLocal) => {
    if (typeof setSelectedData !== 'function') return;
    setSelectedData((prev) => ({
      ...(prev ?? {}),
      vendorReceivedFrom: rightLocal.map(toParentShape),
    }));
  };

  // ---- state ----------------------------------------------------------------

  const [availableFileReceivedFrom, setAvailableFileReceivedFrom] = useState([]);   // LEFT
  const [moveAvailableFileReceivedFrom, setMoveAvailableFileReceivedFrom] = useState([]); // RIGHT
  const [selectedAvailIds, setSelectedAvailIds] = useState([]); // LEFT selected ids
  const [selectedSentIds, setSelectedSentIds] = useState([]);   // RIGHT selected ids

  const [activeSide, setActiveSide] = useState('left'); // 'left' | 'right'
  const [adding, setAdding] = useState(false);
  const [removing, setRemoving] = useState(false);

  // ---- seed RIGHT from parent ------------------------------------------------

  useEffect(() => {
    const seeded = (selectedData?.vendorReceivedFrom ?? [])
      .map(normalizeVendor)
      .filter(v => v.vendId);
    setMoveAvailableFileReceivedFrom(seeded);
    setSelectedSentIds([]); // clear selection on parent change
  }, [selectedData?.vendorReceivedFrom]);

  // ---- load LEFT (fileIo=O) & exclude items already on RIGHT ----------------

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

  // ---- API helpers ----------------------------------------------------------

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

  // ---- checkbox logic (tri-state) ------------------------------------------

  const selectedLeftVendors = useMemo(
    () => availableFileReceivedFrom.filter(v => selectedAvailIds.includes(v.vendId)),
    [availableFileReceivedFrom, selectedAvailIds]
  );
  const selectedRightVendors = useMemo(
    () => moveAvailableFileReceivedFrom.filter(v => selectedSentIds.includes(v.vendId)),
    [moveAvailableFileReceivedFrom, selectedSentIds]
  );

  const leftAllTrue = selectedLeftVendors.length > 0 && selectedLeftVendors.every(v => v.queueForMail === true);
  const leftAllFalse = selectedLeftVendors.length > 0 && selectedLeftVendors.every(v => v.queueForMail === false);
  const leftIndeterminate = selectedLeftVendors.length > 1 && !leftAllTrue && !leftAllFalse;

  const rightAllTrue = selectedRightVendors.length > 0 && selectedRightVendors.every(v => v.queueForMail === true);
  const rightAllFalse = selectedRightVendors.length > 0 && selectedRightVendors.every(v => v.queueForMail === false);
  const rightIndeterminate = selectedRightVendors.length > 1 && !rightAllTrue && !rightAllFalse;

  const isIndeterminate = activeSide === 'left' ? leftIndeterminate : rightIndeterminate;
  const currentChecked =
    activeSide === 'left'
      ? (selectedLeftVendors.length === 0 ? false : leftAllTrue)
      : (selectedRightVendors.length === 0 ? false : rightAllTrue);

  // RIGHT: enable only if selection exists AND all selected have queueForMail=true
  const rightEnableCondition = selectedRightVendors.length > 0 && rightAllTrue;

  const checkboxDisabled =
    !isEditable ||
    adding ||
    removing ||
    (
      activeSide === 'left'
        ? selectedAvailIds.length === 0
        : !rightEnableCondition
    );

  const checkboxRef = useRef(null);
  useEffect(() => {
    if (checkboxRef.current) {
      checkboxRef.current.indeterminate = isIndeterminate;
    }
  }, [isIndeterminate, activeSide, selectedLeftVendors.length, selectedRightVendors.length]);

  const handleToggleQueueForMail = (e) => {
    const nextChecked = e.target.checked;
    if (activeSide === 'right') return; // read-only on RIGHT
    if (selectedLeftVendors.length === 0) return;

    setAvailableFileReceivedFrom(prev =>
      prev.map(v => (selectedAvailIds.includes(v.vendId) ? { ...v, queueForMail: nextChecked } : v))
    );
  };

  // ---- Add -> API -> move LEFT -> RIGHT & sync parent ----------------------

  const handleAdd = async () => {
    if (!isEditable || selectedAvailIds.length === 0) return;

    const sysPrin = selectedData?.sysPrin || '';
    if (!sysPrin) {
      alert('Missing sysPrin on the current record.');
      return;
    }

    setAdding(true);
    try {
      const toAdd = availableFileReceivedFrom.filter(v => selectedAvailIds.includes(v.vendId));

      const results = await Promise.allSettled(
        toAdd.map(v =>
          postAddReceivedFrom(sysPrin, v.vendId, v.queueForMail)
            .then(() => ({ ok: true, vendor: v }))
            .catch((err) => ({ ok: false, vendor: v, error: err?.message || 'Failed' }))
        )
      );

      const successes = results
        .filter(r => (r.status === 'fulfilled' ? r.value.ok : r.value?.ok))
        .map(r => (r.status === 'fulfilled' ? r.value.vendor : r.value.vendor));
      const failures = results.filter(r => !(r.status === 'fulfilled' ? r.value.ok : r.value?.ok));

      if (successes.length > 0) {
        // Update LEFT pool (remove successes)
        const successIds = new Set(successes.map(v => v.vendId));
        setAvailableFileReceivedFrom(prev => prev.filter(v => !successIds.has(v.vendId)));

        // Update RIGHT list
        setMoveAvailableFileReceivedFrom(prev => {
          const ids = new Set(prev.map(p => p.vendId));
          const merged = [...prev, ...successes.filter(x => !ids.has(x.vendId))];

          // Sync parent
          syncRightToParent(merged);

          return merged;
        });
      }

      if (failures.length > 0) {
        const msg = failures.map(f => `${f.value.vendor?.vendId || '?'}: ${f.value.error || 'Failed'}`).join('\n');
        alert(`Some vendors could not be added:\n${msg}`);
      }

      setSelectedAvailIds([]);
      setActiveSide('left');
    } finally {
      setAdding(false);
    }
  };

  // ---- Remove -> API -> move RIGHT -> LEFT & sync parent --------------------

  const handleRemove = async () => {
    if (!isEditable || selectedSentIds.length === 0) return;

    const sysPrin = selectedData?.sysPrin || '';
    if (!sysPrin) {
      alert('Missing sysPrin on the current record.');
      return;
    }

    setRemoving(true);
    try {
      const toDelete = moveAvailableFileReceivedFrom.filter(v => selectedSentIds.includes(v.vendId));

      const results = await Promise.allSettled(
        toDelete.map(v =>
          deleteReceivedFrom(sysPrin, v.vendId, v.queueForMail)
            .then(() => ({ ok: true, vendor: v }))
            .catch((err) => ({ ok: false, vendor: v, error: err?.message || 'Failed' }))
        )
      );

      const successes = results
        .filter(r => (r.status === 'fulfilled' ? r.value.ok : r.value?.ok))
        .map(r => (r.status === 'fulfilled' ? r.value.vendor : r.value.vendor));
      const failures = results.filter(r => !(r.status === 'fulfilled' ? r.value.ok : r.value?.ok));

      if (successes.length > 0) {
        const successIds = new Set(successes.map(v => v.vendId));

        // RIGHT: remove successes
        const remainingRight = moveAvailableFileReceivedFrom.filter(v => !successIds.has(v.vendId));
        setMoveAvailableFileReceivedFrom(remainingRight);

        // LEFT: return removed to pool (avoid duplicates)
        setAvailableFileReceivedFrom(prev => {
          const ids = new Set(prev.map(p => p.vendId));
          const merged = [...prev, ...successes.filter(x => !ids.has(x.vendId))];
          return merged;
        });

        // Sync parent
        syncRightToParent(remainingRight);
      }

      if (failures.length > 0) {
        const msg = failures.map(f => `${f.value.vendor?.vendId || '?'}: ${f.value.error || 'Failed'}`).join('\n');
        alert(`Some vendors could not be removed:\n${msg}`);
      }

      setSelectedSentIds([]);
      setActiveSide('right');
    } finally {
      setRemoving(false);
    }
  };

  // ---- styles ---------------------------------------------------------------

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
  const buttonStyle = { width: '120px', fontSize: '0.78rem' };

  // ---- render ---------------------------------------------------------------

  return (
    <CRow>
      <CCol xs={12}>
        <CCard className="mb-4">
          <CCardBody>
            <CRow className="align-items-center">
              {/* LEFT */}
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

              {/* MIDDLE */}
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

              {/* RIGHT */}
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
