import React, { useEffect, useMemo, useRef, useState } from 'react';
import {
  CCard, CCardBody, CCol, CRow, CButton, CFormSelect, CFormCheck,
} from '@coreui/react';

/**
 * EditFileSentTo
 *
 * Props:
 * - selectedData: { sysPrin, vendorSentTo: [...] , ... }
 * - setSelectedData?: function(prev => next)        // optional fallback
 * - isEditable: boolean
 * - onChangeVendorSentTo?: function(updaterOrArray) // focused updater (preferred)
 */

const EditFileSentTo = ({
  selectedData,
  setSelectedData,        // optional fallback
  isEditable,
  onChangeVendorSentTo,   // focused updater from parent
}) => {
  // ---------- helpers ----------
  const normalize = (v = {}) => ({
    vendId: String(v?.vendId ?? v?.vendorId ?? v?.vendor?.vendId ?? v?.vendor?.id ?? ''),
    vendName: v?.vendName ?? v?.vendorName ?? v?.vendor?.vendNm ?? v?.vendor?.name ?? String(v?.vendId ?? v?.vendorId ?? ''),
    queueForMail: (() => {
      const raw = v?.queueForMail ?? v?.queForMail ?? v?.que_for_mail ?? v?.queForMailCd ?? v?.queue_for_mail_cd;
      if (typeof raw === 'string') {
        const s = raw.trim().toUpperCase();
        return s === '1' || s === 'Y' || s === 'TRUE';
      }
      if (typeof raw === 'number') return raw === 1;
      return Boolean(raw);
    })(),
  });

  const toParentShape = (list) =>
    list.map(v => ({
      vendorId: v.vendId,
      vendId: v.vendId,
      vendName: v.vendName,
      queueForMail: !!v.queueForMail,
      queForMail: !!v.queueForMail,
      queForMailCd: v.queueForMail ? 'Y' : 'N',
      vendor: { vendId: v.vendId, vendNm: v.vendName },
      ...(selectedData?.sysPrin
        ? { id: { sysPrin: String(selectedData.sysPrin), vendorId: v.vendId } }
        : {}),
    }));

  // Push updates to parent (use focused updater if present; fallback to setSelectedData)
  const pushRightToParent = (rightList) => {
    if (typeof onChangeVendorSentTo === 'function') {
      onChangeVendorSentTo(rightList);
      return;
    }
    if (typeof setSelectedData === 'function') {
      setSelectedData(prev => ({ ...(prev ?? {}), vendorSentTo: rightList }));
    }
  };

  // ---------- state ----------
  const sysPrin = String(selectedData?.sysPrin ?? '');
  const [allAvailable, setAllAvailable] = useState([]); // master pool (fileIo=I)
  const [right, setRight] = useState([]);               // assigned list ("sent-to")

  const [selLeftIds, setSelLeftIds] = useState([]);
  const [selRightIds, setSelRightIds] = useState([]);
  const [activeSide, setActiveSide] = useState('left');

  const [saving, setSaving] = useState(false);
  const [removing, setRemoving] = useState(false);

  // ---------- seed RIGHT from parent slice (DEPEND ON THE SLICE) ------------
  useEffect(() => {
    const seeded = (selectedData?.vendorSentTo ?? []).map(normalize).filter(v => v.vendId);
    setRight(seeded);
    setSelRightIds([]);
  }, [selectedData?.vendorSentTo]);

  // ---------- load master available (fileIo=I) ----------
  useEffect(() => {
    fetch('http://localhost:8089/client-sysprin-reader/api/vendor?fileIo=I')
      .then(r => r.json())
      .then(data => setAllAvailable((Array.isArray(data) ? data : []).map(normalize)))
      .catch(err => console.error('Failed to load vendors (I):', err));
  }, [sysPrin]);

  // ---------- derive LEFT by subtracting RIGHT ----------
  const left = useMemo(() => {
    const rightIds = new Set(right.map(v => v.vendId));
    return allAvailable.filter(v => !rightIds.has(v.vendId));
  }, [allAvailable, right]);

  // ---------- selected groups + tri-state ----------
  const selectedLeft = useMemo(() => left.filter(v => selLeftIds.includes(v.vendId)), [left, selLeftIds]);
  const selectedRight = useMemo(() => right.filter(v => selRightIds.includes(v.vendId)), [right, selRightIds]);

  const leftAllTrue = selectedLeft.length > 0 && selectedLeft.every(v => v.queueForMail);
  const leftAllFalse = selectedLeft.length > 0 && selectedLeft.every(v => !v.queueForMail);
  const leftInd = selectedLeft.length > 1 && !leftAllTrue && !leftAllFalse;

  const rightAllTrue = selectedRight.length > 0 && selectedRight.every(v => v.queueForMail);
  const rightAllFalse = selectedRight.length > 0 && selectedRight.every(v => !v.queueForMail);
  const rightInd = selectedRight.length > 1 && !rightAllTrue && !rightAllFalse;

  const isIndeterminate = activeSide === 'left' ? leftInd : rightInd;
  const currentChecked =
    activeSide === 'left'
      ? (selectedLeft.length === 0 ? false : leftAllTrue)
      : (selectedRight.length === 0 ? false : rightAllTrue);

  const checkboxRef = useRef(null);
  useEffect(() => {
    if (checkboxRef.current) checkboxRef.current.indeterminate = isIndeterminate;
  }, [isIndeterminate]);

  // RIGHT checkbox only enabled if all selected on the right are true (read-only indicator)
  const rightEnableCondition = selectedRight.length > 0 && rightAllTrue;
  const checkboxDisabled =
    !isEditable || saving || removing ||
    (activeSide === 'left' ? selectedLeft.length === 0 : !rightEnableCondition);

  // ---------- checkbox toggle (LEFT only) ----------
  const onToggleQueue = (e) => {
    if (activeSide === 'right') return;
    const next = !!e.target.checked;
    setAllAvailable(prev =>
      prev.map(v => (selLeftIds.includes(v.vendId) ? { ...v, queueForMail: next } : v))
    );
  };

  // ---------- API helpers ----------
  const postAdd = async (sysPrin, vendorId, queForMail) => {
    const url = `http://localhost:8089/client-sysprin-writer/api/sysprins/${encodeURIComponent(sysPrin)}/sent-to/create`;
    const res = await fetch(url, {
      method: 'POST',
      headers: { accept: '*/*', 'Content-Type': 'application/json' },
      body: JSON.stringify({ sysPrin, vendorId, queForMail }),
    });
    if (!res.ok) throw new Error(await res.text());
    return true;
  };

  const delRemove = async (sysPrin, vendorId, queForMail) => {
    const url = `http://localhost:8089/client-sysprin-writer/api/sysprins/${encodeURIComponent(sysPrin)}/sent-to/delete`;
    const res = await fetch(url, {
      method: 'DELETE',
      headers: { accept: '*/*', 'Content-Type': 'application/json' },
      body: JSON.stringify({ sysPrin, vendorId, queForMail }),
    });
    if (!res.ok) throw new Error(await res.text());
    return true;
  };

  // ---------- SAVE (LEFT -> RIGHT) ----------
  const handleSave = async () => {
    if (!isEditable || selLeftIds.length === 0 || !sysPrin) return;

    setSaving(true);
    try {
      const toAdd = left.filter(v => selLeftIds.includes(v.vendId));

      const results = await Promise.allSettled(
        toAdd.map(v => postAdd(sysPrin, v.vendId, v.queueForMail).then(() => v))
      );
      const successes = results.filter(r => r.status === 'fulfilled').map(r => r.value);
      const failed = results.filter(r => r.status === 'rejected');
      if (failed.length) {
        alert(`Some failed:\n${failed.map((f, i) => `#${i+1}: ${f.reason}`).join('\n')}`);
      }

      if (successes.length) {
        setRight(prev => {
          const ids = new Set(prev.map(x => x.vendId));
          const merged = [...prev, ...successes.filter(s => !ids.has(s.vendId))];
          pushRightToParent(toParentShape(merged)); // sync parent
          return merged;
        });
      }

      setSelLeftIds([]);
      setActiveSide('left');
    } finally {
      setSaving(false);
    }
  };

  // ---------- REMOVE (RIGHT -> keep out of RIGHT) ----------
  const handleRemove = async () => {
    if (!isEditable || selRightIds.length === 0 || !sysPrin) return;

    setRemoving(true);
    try {
      const toRemove = right.filter(v => selRightIds.includes(v.vendId));

      const results = await Promise.allSettled(
        toRemove.map(v => delRemove(sysPrin, v.vendId, v.queueForMail).then(() => v))
      );
      const successes = results.filter(r => r.status === 'fulfilled').map(r => r.value);
      const failed = results.filter(r => r.status === 'rejected');
      if (failed.length) {
        alert(`Some failed:\n${failed.map((f, i) => `#${i+1}: ${f.reason}`).join('\n')}`);
      }

      if (successes.length) {
        const successIds = new Set(successes.map(v => v.vendId));
        const remainingRight = right.filter(v => !successIds.has(v.vendId));
        setRight(remainingRight);
        pushRightToParent(toParentShape(remainingRight)); // sync parent
      }

      setSelRightIds([]);
      setActiveSide('right');
    } finally {
      setRemoving(false);
    }
  };

  // ---------- styles ----------
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
              {/* LEFT: derived available (allAvailable - right) */}
              <CCol md={5} className="order-md-1">
                <CFormSelect
                  multiple
                  size="10"
                  style={selectStyle}
                  value={selLeftIds}
                  onChange={(e) => {
                    setSelLeftIds([...e.target.selectedOptions].map(o => o.value));
                    setActiveSide('left');
                  }}
                  onClick={() => setActiveSide('left')}
                  disabled={!isEditable || saving || removing}
                >
                  {left.map(v => (
                    <option key={v.vendId} value={v.vendId} style={optionStyle}>
                      {v.vendId} — {v.vendName} {v.queueForMail ? ' (Q)' : ''}
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
                  disabled={!isEditable || selLeftIds.length === 0 || saving || removing}
                >
                  {saving ? 'Saving…' : 'Save ⬇️'}
                </CButton>

                {/* Queue for mail (editable on LEFT, read-only indicator on RIGHT) */}
                <div style={{ display: 'flex', alignItems: 'center', gap: 8, fontSize: '0.72rem', lineHeight: 1.1 }}>
                  <CFormCheck
                    id="queueForMailSentTo"
                    checked={currentChecked}
                    onChange={onToggleQueue}
                    disabled={checkboxDisabled}
                    inputRef={checkboxRef}
                    label=""
                  />
                  <label htmlFor="queueForMailSentTo" style={{ margin: 0, cursor: checkboxDisabled ? 'default' : 'pointer' }}>
                    Queue for mail
                  </label>
                </div>

                <CButton
                  color="danger"
                  variant="outline"
                  size="sm"
                  style={buttonStyle}
                  onClick={handleRemove}
                  disabled={!isEditable || selRightIds.length === 0 || saving || removing}
                >
                  {removing ? 'Removing…' : '⬆️ Remove'}
                </CButton>
              </CCol>

              {/* RIGHT: assigned (seeded from parent, synced on save/remove) */}
              <CCol md={5} className="order-md-3 d-flex justify-content-end">
                <CFormSelect
                  multiple
                  size="10"
                  style={selectStyle}
                  value={selRightIds}
                  onChange={(e) => {
                    setSelRightIds([...e.target.selectedOptions].map(o => o.value));
                    setActiveSide('right');
                  }}
                  onClick={() => setActiveSide('right')}
                  disabled={!isEditable || saving || removing}
                >
                  {right.map(v => (
                    <option key={v.vendId} value={v.vendId} style={optionStyle}>
                      {v.vendId} — {v.vendName} {v.queueForMail ? ' (Q)' : ''}
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







import React, { useEffect, useMemo, useRef, useState } from 'react';
import { CCard, CCardBody, CCol, CRow, CButton, CFormSelect, CFormCheck } from '@coreui/react';

const EditFileReceivedFrom = ({
  selectedData,
  onChangeVendorReceivedFrom, // focused updater from parent
  isEditable,
  setSelectedData,            // optional fallback if focused updater isn't provided
}) => {
  // --- helpers ---------------------------------------------------------------
  const normalize = (v = {}) => ({
    vendId: String(v?.vendId ?? v?.vendorId ?? v?.vendor?.vendId ?? v?.vendor?.id ?? ''),
    vendName: v?.vendName ?? v?.vendorName ?? v?.vendor?.vendNm ?? v?.vendor?.name ?? String(v?.vendId ?? v?.vendorId ?? ''),
    queueForMail: (() => {
      const raw = v?.queueForMail ?? v?.queForMail ?? v?.que_for_mail ?? v?.queForMailCd ?? v?.queue_for_mail_cd;
      if (typeof raw === 'string') {
        const s = raw.trim().toUpperCase();
        return s === '1' || s === 'Y' || s === 'TRUE';
      }
      if (typeof raw === 'number') return raw === 1;
      return Boolean(raw);
    })(),
  });

  const toParentShape = (list) =>
    list.map(v => ({
      vendorId: v.vendId,
      vendId: v.vendId,
      vendName: v.vendName,
      queueForMail: !!v.queueForMail,
      queForMail: !!v.queueForMail,
      queForMailCd: v.queueForMail ? 'Y' : 'N',
      vendor: { vendId: v.vendId, vendNm: v.vendName },
      ...(selectedData?.sysPrin
        ? { id: { sysPrin: String(selectedData.sysPrin), vendorId: v.vendId } }
        : {}),
    }));

  // Push updates to parent (use focused updater if present; fallback to setSelectedData)
  const pushRightToParent = (rightList) => {
    if (typeof onChangeVendorReceivedFrom === 'function') {
      onChangeVendorReceivedFrom(rightList);
      return;
    }
    if (typeof setSelectedData === 'function') {
      setSelectedData(prev => ({ ...(prev ?? {}), vendorReceivedFrom: rightList }));
    }
  };

  // --- state -----------------------------------------------------------------
  const sysPrin = String(selectedData?.sysPrin ?? '');
  const [allAvailable, setAllAvailable] = useState([]); // master pool (fileIo=O)
  const [right, setRight] = useState([]);               // assigned list
  const [selLeftIds, setSelLeftIds] = useState([]);
  const [selRightIds, setSelRightIds] = useState([]);
  const [activeSide, setActiveSide] = useState('left');
  const [adding, setAdding] = useState(false);
  const [removing, setRemoving] = useState(false);

  // seed RIGHT from parent slice (IMPORTANT: depend on the slice itself)
  useEffect(() => {
    const seeded = (selectedData?.vendorReceivedFrom ?? []).map(normalize).filter(v => v.vendId);
    setRight(seeded);
    setSelRightIds([]);
  }, [selectedData?.vendorReceivedFrom]);

  // load master available vendors (fileIo=O)
  useEffect(() => {
    fetch('http://localhost:8089/client-sysprin-reader/api/vendor?fileIo=O')
      .then(r => r.json())
      .then(data => setAllAvailable((Array.isArray(data) ? data : []).map(normalize)))
      .catch(err => console.error('Failed to load vendors (O):', err));
  }, [sysPrin]);

  // derive LEFT by subtracting RIGHT
  const left = useMemo(() => {
    const rightIds = new Set(right.map(v => v.vendId));
    return allAvailable.filter(v => !rightIds.has(v.vendId));
  }, [allAvailable, right]);

  // selections + tri-state
  const selectedLeft = useMemo(() => left.filter(v => selLeftIds.includes(v.vendId)), [left, selLeftIds]);
  const selectedRight = useMemo(() => right.filter(v => selRightIds.includes(v.vendId)), [right, selRightIds]);

  const leftAllTrue = selectedLeft.length > 0 && selectedLeft.every(v => v.queueForMail);
  const leftAllFalse = selectedLeft.length > 0 && selectedLeft.every(v => !v.queueForMail);
  const leftInd = selectedLeft.length > 1 && !leftAllTrue && !leftAllFalse;

  const rightAllTrue = selectedRight.length > 0 && selectedRight.every(v => v.queueForMail);
  const rightAllFalse = selectedRight.length > 0 && selectedRight.every(v => !v.queueForMail);
  const rightInd = selectedRight.length > 1 && !rightAllTrue && !rightAllFalse;

  const isIndeterminate = activeSide === 'left' ? leftInd : rightInd;
  const currentChecked =
    activeSide === 'left'
      ? (selectedLeft.length === 0 ? false : leftAllTrue)
      : (selectedRight.length === 0 ? false : rightAllTrue);

  const rightEnableCondition = selectedRight.length > 0 && rightAllTrue;
  const checkboxDisabled =
    !isEditable || adding || removing ||
    (activeSide === 'left' ? selectedLeft.length === 0 : !rightEnableCondition);

  const checkboxRef = useRef(null);
  useEffect(() => {
    if (checkboxRef.current) checkboxRef.current.indeterminate = isIndeterminate;
  }, [isIndeterminate]);

  // LEFT-only toggle
  const onToggleQueue = (e) => {
    if (activeSide === 'right') return;
    const next = !!e.target.checked;
    setAllAvailable(prev => prev.map(v => (selLeftIds.includes(v.vendId) ? { ...v, queueForMail: next } : v)));
  };

  // API helpers
  const postAdd = async (sysPrin, vendorId, queForMail) => {
    const url = `http://localhost:8089/client-sysprin-writer/api/sysprins/${encodeURIComponent(sysPrin)}/received-from/create`;
    const res = await fetch(url, {
      method: 'POST',
      headers: { accept: '*/*', 'Content-Type': 'application/json' },
      body: JSON.stringify({ sysPrin, vendorId, queForMail }),
    });
    if (!res.ok) throw new Error(await res.text());
    return true;
  };
  const delRemove = async (sysPrin, vendorId, queForMail) => {
    const url = `http://localhost:8089/client-sysprin-writer/api/sysprins/${encodeURIComponent(sysPrin)}/received-from/delete`;
    const res = await fetch(url, {
      method: 'DELETE',
      headers: { accept: '*/*', 'Content-Type': 'application/json' },
      body: JSON.stringify({ sysPrin, vendorId, queForMail }),
    });
    if (!res.ok) throw new Error(await res.text());
    return true;
  };

  // ADD (LEFT -> RIGHT)
  const handleAdd = async () => {
    if (!isEditable || selLeftIds.length === 0 || !sysPrin) return;
    setAdding(true);
    try {
      const toAdd = left.filter(v => selLeftIds.includes(v.vendId));
      const results = await Promise.allSettled(toAdd.map(v => postAdd(sysPrin, v.vendId, v.queueForMail).then(() => v)));
      const successes = results.filter(r => r.status === 'fulfilled').map(r => r.value);
      const failed    = results.filter(r => r.status === 'rejected');
      if (failed.length) alert(`Some failed:\n${failed.map((f,i)=>`#${i+1}: ${f.reason}`).join('\n')}`);

      if (successes.length) {
        setRight(prev => {
          const ids = new Set(prev.map(x => x.vendId));
          const merged = [...prev, ...successes.filter(s => !ids.has(s.vendId))];
          pushRightToParent(toParentShape(merged));   // <- sync parent
          return merged;
        });
      }

      setSelLeftIds([]);
      setActiveSide('left');
    } finally {
      setAdding(false);
    }
  };

  // REMOVE (RIGHT -> remove)
  const handleRemove = async () => {
    if (!isEditable || selRightIds.length === 0 || !sysPrin) return;
    setRemoving(true);
    try {
      const toRemove = right.filter(v => selRightIds.includes(v.vendId));
      const results = await Promise.allSettled(toRemove.map(v => delRemove(sysPrin, v.vendId, v.queueForMail).then(() => v)));
      const successes = results.filter(r => r.status === 'fulfilled').map(r => r.value);
      const failed    = results.filter(r => r.status === 'rejected');
      if (failed.length) alert(`Some failed:\n${failed.map((f,i)=>`#${i+1}: ${f.reason}`).join('\n')}`);

      if (successes.length) {
        const successIds = new Set(successes.map(v => v.vendId));
        const remainingRight = right.filter(v => !successIds.has(v.vendId));
        setRight(remainingRight);
        pushRightToParent(toParentShape(remainingRight)); // <- sync parent
      }

      setSelRightIds([]);
      setActiveSide('right');
    } finally {
      setRemoving(false);
    }
  };

  // styles
  const selectStyle = { height: '350px', fontSize: '0.78rem', width: '100%', maxWidth: '350px', paddingLeft: '16px', scrollbarWidth: 'none', msOverflowStyle: 'none' };
  const optionStyle = { fontSize: '0.78rem', borderBottom: '1px dotted #ccc', padding: '4px 6px' };
  const buttonStyle = { width: '120px', fontSize: '0.78rem' };

  return (
    <CRow>
      <CCol xs={12}>
        <CCard className="mb-4">
          <CCardBody>
            <CRow className="align-items-center">
              {/* LEFT */}
              <CCol md={5}>
                <CFormSelect
                  multiple size="10" style={selectStyle}
                  value={selLeftIds}
                  onChange={(e) => {
                    setSelLeftIds([...e.target.selectedOptions].map(o => o.value));
                    setActiveSide('left');
                  }}
                  onClick={() => setActiveSide('left')}
                  disabled={!isEditable || adding || removing}
                >
                  {left.map(v => (
                    <option key={v.vendId} value={v.vendId} style={optionStyle}>
                      {v.vendId} — {v.vendName} {v.queueForMail ? ' (Q)' : ''}
                    </option>
                  ))}
                </CFormSelect>
              </CCol>

              {/* MIDDLE */}
              <CCol md={2} className="d-flex flex-column align-items-center justify-content-center" style={{ minHeight: 200, gap: 24 }}>
                <CButton color="success" variant="outline" size="sm" style={buttonStyle}
                         onClick={handleAdd}
                         disabled={!isEditable || selLeftIds.length === 0 || adding || removing}>
                  {adding ? 'Adding…' : 'Add ⬇️'}
                </CButton>

                <div style={{ display: 'flex', alignItems: 'center', gap: 8, fontSize: '0.72rem', lineHeight: 1.1 }}>
                  <CFormCheck
                    id="queueForMail"
                    checked={currentChecked}
                    onChange={onToggleQueue}
                    disabled={checkboxDisabled}
                    inputRef={checkboxRef}
                    label=""
                  />
                  <label htmlFor="queueForMail" style={{ margin: 0, cursor: checkboxDisabled ? 'default' : 'pointer' }}>
                    Queue for mail
                  </label>
                </div>

                <CButton color="danger" variant="outline" size="sm" style={buttonStyle}
                         onClick={handleRemove}
                         disabled={!isEditable || selRightIds.length === 0 || adding || removing}>
                  {removing ? 'Removing…' : '⬆️ Remove'}
                </CButton>
              </CCol>

              {/* RIGHT */}
              <CCol md={5} className="d-flex justify-content-end">
                <CFormSelect
                  multiple size="10" style={selectStyle}
                  value={selRightIds}
                  onChange={(e) => {
                    setSelRightIds([...e.target.selectedOptions].map(o => o.value));
                    setActiveSide('right');
                  }}
                  onClick={() => setActiveSide('right')}
                  disabled={!isEditable || adding || removing}
                >
                  {right.map(v => (
                    <option key={v.vendId} value={v.vendId} style={optionStyle}>
                      {v.vendId} — {v.vendName} {v.queueForMail ? ' (Q)' : ''}
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



