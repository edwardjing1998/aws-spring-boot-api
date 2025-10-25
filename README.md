import React, { useEffect, useMemo, useRef, useState } from 'react';
import {
  CCard, CCardBody, CCol, CRow, CButton, CFormSelect, CFormCheck,
} from '@coreui/react';

const EditFileSentTo = ({ selectedData, setSelectedData, isEditable }) => {
  // normalize to { vendId, vendName, queueForMail }
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

  const [availableSentFileTo, setAvailableSentFileTo] = useState([]);
  const [moveAvailableSentFileTo, setMoveAvailableSentFileto] = useState([]);
  const [selectedAvailIds, setSelectedAvailIds] = useState([]);
  const [selectedSentIds, setSelectedSentIds] = useState([]);

  // track which side is active: 'left' | 'right'
  const [activeSide, setActiveSide] = useState('left');

  // busy flags
  const [saving, setSaving] = useState(false);
  const [removing, setRemoving] = useState(false);

  // seed RIGHT from selectedData.vendorSentTo
  useEffect(() => {
    const right = (selectedData?.vendorSentTo ?? [])
      .map(normalizeVendor)
      .filter(v => v.vendId);
    setMoveAvailableSentFileto(right);
    setSelectedSentIds([]);
  }, [selectedData?.vendorSentTo]);

  // load LEFT (available vendors for fileIo=I)
  useEffect(() => {
    fetch('http://localhost:8089/client-sysprin-reader/api/vendor?fileIo=I')
      .then((r) => r.json())
      .then((data) => {
        const left = (Array.isArray(data) ? data : []).map(normalizeVendor);
        setAvailableSentFileTo(left);
      })
      .catch((err) => console.error('Failed to load vendors (SentTo)', err));
  }, []);

  // derived LEFT that excludes anything already on RIGHT
  const leftFiltered = useMemo(() => {
    const rightIds = new Set(moveAvailableSentFileTo.map(v => v.vendId));
    return availableSentFileTo.filter(v => !rightIds.has(v.vendId));
  }, [availableSentFileTo, moveAvailableSentFileTo]);

  // ===== API helpers =====
  async function postAddSentTo(sysPrin, vendorId, queForMail) {
    const url = `http://localhost:8089/client-sysprin-writer/api/sysprins/${encodeURIComponent(sysPrin)}/sent-to/create`;
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

  async function deleteSentTo(sysPrin, vendorId, queForMail) {
    const url = `http://localhost:8089/client-sysprin-writer/api/sysprins/${encodeURIComponent(sysPrin)}/sent-to/delete`;
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

  // ===== Checkbox (queueForMail) for BOTH sides (read-only for RIGHT) =====
  const selectedLeftVendors = useMemo(
    () => leftFiltered.filter(v => selectedAvailIds.includes(v.vendId)),
    [leftFiltered, selectedAvailIds]
  );
  const selectedRightVendors = useMemo(
    () => moveAvailableSentFileTo.filter(v => selectedSentIds.includes(v.vendId)),
    [moveAvailableSentFileTo, selectedSentIds]
  );

  // tri-state LEFT
  const leftAllTrue = selectedLeftVendors.length > 0 && selectedLeftVendors.every(v => v.queueForMail === true);
  const leftAllFalse = selectedLeftVendors.length > 0 && selectedLeftVendors.every(v => v.queueForMail === false);
  const leftIndeterminate = selectedLeftVendors.length > 1 && !leftAllTrue && !leftAllFalse;

  // tri-state RIGHT (read-only)
  const rightAllTrue = selectedRightVendors.length > 0 && selectedRightVendors.every(v => v.queueForMail === true);
  const rightAllFalse = selectedRightVendors.length > 0 && selectedRightVendors.every(v => v.queueForMail === false);
  const rightIndeterminate = selectedRightVendors.length > 1 && !rightAllTrue && !rightAllFalse;

  const isIndeterminate = activeSide === 'left' ? leftIndeterminate : rightIndeterminate;
  const currentChecked =
    activeSide === 'left'
      ? (selectedLeftVendors.length === 0 ? false : leftAllTrue)
      : (selectedRightVendors.length === 0 ? false : rightAllTrue);

  // RIGHT: enable checkbox only if there is a selection AND all selected have queueForMail=true
  const rightEnableCondition = selectedRightVendors.length > 0 && rightAllTrue;

  const checkboxDisabled =
    !isEditable ||
    saving ||
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

    // Update the underlying LEFT pool (not just the filtered view)
    setAvailableSentFileTo(prev => {
      const rightIds = new Set(moveAvailableSentFileTo.map(v => v.vendId));
      return prev.map(v => {
        if (rightIds.has(v.vendId)) return v; // don't touch right items
        if (selectedAvailIds.includes(v.vendId)) return { ...v, queueForMail: nextChecked };
        return v;
      });
    });
  };

  // ===== Save (POST) to RIGHT =====
  const handleSave = async () => {
    if (!isEditable || selectedAvailIds.length === 0) return;
    const sysPrin = selectedData?.sysPrin || '';
    if (!sysPrin) {
      alert('Missing sysPrin on the current record.');
      return;
    }

    setSaving(true);
    try {
      const toSave = leftFiltered.filter(v => selectedAvailIds.includes(v.vendId));
      const results = await Promise.allSettled(
        toSave.map(v =>
          postAddSentTo(sysPrin, v.vendId, v.queueForMail)
            .then(() => ({ ok: true, vendor: v }))
            .catch((err) => ({ ok: false, vendor: v, error: err?.message || 'Failed' }))
        )
      );

      const successes = results
        .filter(r => (r.status === 'fulfilled' ? r.value.ok : r.value?.ok))
        .map(r => (r.status === 'fulfilled' ? r.value.vendor : r.value.vendor));
      const failures = results.filter(r => !(r.status === 'fulfilled' ? r.value.ok : r.value?.ok));

      if (successes.length > 0) {
        // Update RIGHT list and selectedData.vendorSentTo immutably
        setMoveAvailableSentFileto(prev => {
          const ids = new Set(prev.map(p => p.vendId));
          const merged = [...prev, ...successes.filter(t => !ids.has(t.vendId))];
          setSelectedData?.(p => ({ ...p, vendorSentTo: merged }));
          return merged;
        });
      }

      if (failures.length > 0) {
        const msg = failures.map(f => `${f.value.vendor?.vendId || '?'}: ${f.value.error || 'Failed'}`).join('\n');
        alert(`Some vendors could not be saved:\n${msg}`);
      }

      setSelectedAvailIds([]);
      setActiveSide('left');
    } finally {
      setSaving(false);
    }
  };

  // ===== Remove (DELETE) back to LEFT =====
  const handleRemove = async () => {
    if (!isEditable || selectedSentIds.length === 0) return;
    const sysPrin = selectedData?.sysPrin || '';
    if (!sysPrin) {
      alert('Missing sysPrin on the current record.');
      return;
    }

    setRemoving(true);
    try {
      const toRemove = moveAvailableSentFileTo.filter(v => selectedSentIds.includes(v.vendId));
      const results = await Promise.allSettled(
        toRemove.map(v =>
          deleteSentTo(sysPrin, v.vendId, v.queueForMail)
            .then(() => ({ ok: true, vendor: v }))
            .catch((err) => ({ ok: false, vendor: v, error: err?.message || 'Failed' }))
        )
      );

      const successes = results
        .filter(r => (r.status === 'fulfilled' ? r.value.ok : r.value?.ok))
        .map(r => (r.status === 'fulfilled' ? r.value.vendor : r.value.vendor));
      const failures = results.filter(r => !(r.status === 'fulfilled' ? r.value.ok : r.value?.ok));

      if (successes.length > 0) {
        // Remove from RIGHT and update selectedData
        const successIds = new Set(successes.map(v => v.vendId));
        const remainingRight = moveAvailableSentFileTo.filter(v => !successIds.has(v.vendId));
        setMoveAvailableSentFileto(remainingRight);
        setSelectedData?.(p => ({ ...p, vendorSentTo: remainingRight }));

        // return removed ones to LEFT pool (avoid duplicates)
        setAvailableSentFileTo(prev => {
          const ids = new Set(prev.map(p => p.vendId));
          const merged = [...prev, ...successes.filter(t => !ids.has(t.vendId))];
          return merged;
        });
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

  // styles
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
                  onChange={(e) => {
                    setSelectedAvailIds([...e.target.selectedOptions].map(o => o.value));
                    setActiveSide('left');
                  }}
                  onClick={() => setActiveSide('left')}
                  disabled={!isEditable || saving || removing}
                >
                  {leftFiltered.map(vendor => (
                    <option key={vendor.vendId} value={vendor.vendId} style={optionStyle}>
                      {vendor.vendId} — {vendor.vendName} {vendor.queueForMail ? ' (Q)' : ''}
                    </option>
                  ))}
                </CFormSelect>
              </CCol>

              {/* MIDDLE: Save / Queue / Remove */}
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
                  onClick={handleSave}
                  disabled={!isEditable || selectedAvailIds.length === 0 || saving || removing}
                >
                  {saving ? 'Saving…' : 'Save ⬇️'}
                </CButton>

                {/* Queue for mail (editable on LEFT, read-only view on RIGHT) */}
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
                    id="queueForMailSentTo"
                    checked={currentChecked}
                    onChange={handleToggleQueueForMail}
                    disabled={checkboxDisabled}
                    inputRef={checkboxRef}
                    label=""
                  />
                  <label
                    htmlFor="queueForMailSentTo"
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
                  disabled={!isEditable || selectedSentIds.length === 0 || saving || removing}
                >
                  {removing ? 'Removing…' : '⬆️ Remove'}
                </CButton>
              </CCol>

              {/* RIGHT: already selected (seeded from selectedData) */}
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
                  disabled={!isEditable || saving || removing}
                >
                  {moveAvailableSentFileTo.map(vendor => (
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

export default EditFileSentTo;
