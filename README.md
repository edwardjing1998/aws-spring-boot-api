import React, { useEffect, useMemo, useRef, useState } from 'react';
import {
  CCard, CCardBody, CCol, CRow, CButton, CFormSelect, CFormCheck,
} from '@coreui/react';

const EditFileReceivedFrom = ({ selectedData, setSelectedData, isEditable }) => {
  // helper: normalize any vendor shape to { vendId, vendName, queueForMail }
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
      String(v?.vendId ?? v?.vendorId ?? ''), // fallback to id
    // accept 1/'1'/true/'Y' as truthy flags
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

  const [availableFileReceivedFrom, setAvailableFileReceivedFrom] = useState([]);
  const [moveAvailableFileReceivedFrom, setMoveAvailableFileReceivedFrom] = useState([]);
  const [selectedAvailIds, setSelectedAvailIds] = useState([]);
  const [selectedSentIds, setSelectedSentIds] = useState([]);

  // which side user last interacted with
  const [activeSide, setActiveSide] = useState('left');

  // Busy states for API calls
  const [adding, setAdding] = useState(false);
  const [removing, setRemoving] = useState(false);

  // seed RIGHT list from parent
  useEffect(() => {
    const seeded = (selectedData?.vendorReceivedFrom ?? [])
      .map(normalizeVendor)
      .filter(v => v.vendId);
    setMoveAvailableFileReceivedFrom(seeded);
  }, [selectedData?.vendorReceivedFrom]);

  // load LEFT list, excluding items already on RIGHT
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

  // === API helpers ===
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

  // === Checkbox logic for BOTH sides ===
  const selectedLeftVendors = useMemo(
    () => availableFileReceivedFrom.filter(v => selectedAvailIds.includes(v.vendId)),
    [availableFileReceivedFrom, selectedAvailIds]
  );
  const selectedRightVendors = useMemo(
    () => moveAvailableFileReceivedFrom.filter(v => selectedSentIds.includes(v.vendId)),
    [moveAvailableFileReceivedFrom, selectedSentIds]
  );

  // compute group tri-state for LEFT
  const leftAllTrue = selectedLeftVendors.length > 0 && selectedLeftVendors.every(v => v.queueForMail === true);
  const leftAllFalse = selectedLeftVendors.length > 0 && selectedLeftVendors.every(v => v.queueForMail === false);
  const leftIndeterminate = selectedLeftVendors.length > 1 && !leftAllTrue && !leftAllFalse;

  // compute group tri-state for RIGHT (read-only, but we still show status)
  const rightAllTrue = selectedRightVendors.length > 0 && selectedRightVendors.every(v => v.queueForMail === true);
  const rightAllFalse = selectedRightVendors.length > 0 && selectedRightVendors.every(v => v.queueForMail === false);
  const rightIndeterminate = selectedRightVendors.length > 1 && !rightAllTrue && !rightAllFalse;

  // combined current state depending on active side
  const isIndeterminate = activeSide === 'left' ? leftIndeterminate : rightIndeterminate;
  const currentChecked =
    activeSide === 'left'
      ? (selectedLeftVendors.length === 0 ? false : leftAllTrue)
      : (selectedRightVendors.length === 0 ? false : rightAllTrue);

  // IMPORTANT: when clicking RIGHT, enable checkbox only if selected items have queueForMail=1/true
  // i.e., enabled on RIGHT iff there is a selection AND all selected have queueForMail=true
  const rightEnableCondition = selectedRightVendors.length > 0 && rightAllTrue;

  // Enable/disable rules:
  // - LEFT: enabled when editable and at least one left item selected
  // - RIGHT: enabled when editable and rightEnableCondition is true
  const checkboxDisabled =
    !isEditable ||
    adding ||
    removing ||
    (
      activeSide === 'left'
        ? selectedAvailIds.length === 0
        : !rightEnableCondition
    );

  // apply indeterminate
  const checkboxRef = useRef(null);
  useEffect(() => {
    if (checkboxRef.current) {
      checkboxRef.current.indeterminate = isIndeterminate;
    }
  }, [isIndeterminate, activeSide, selectedLeftVendors.length, selectedRightVendors.length]);

  // Toggling behavior:
  // - LEFT side: toggles queueForMail on LEFT selection (as before)
  // - RIGHT side: currently read-only (you can enable if true, but we don't change it on RIGHT)
  const handleToggleQueueForMail = (e) => {
    const nextChecked = e.target.checked;
    if (activeSide === 'right') return; // read-only on RIGHT per your requirement
    if (selectedLeftVendors.length === 0) return;
    const nextLeft = availableFileReceivedFrom.map(v =>
      selectedAvailIds.includes(v.vendId) ? { ...v, queueForMail: nextChecked } : v
    );
    setAvailableFileReceivedFrom(nextLeft);
  };

  // == Move logic ==
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
            .then(() => ({ ok: true, vendor: v }))
            .catch((err) => ({ ok: false, vendor: v, error: err?.message || 'Failed' }))
        )
      );

      const successes = results.filter(r => (r.status === 'fulfilled' ? r.value.ok : r.value?.ok)).map(r => (r.status === 'fulfilled' ? r.value.vendor : r.value.vendor));
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
        const msg = failures.map(f => `${f.value.vendor?.vendId || '?'}: ${f.value.error || 'Failed'}`).join('\n');
        alert(`Some vendors could not be added:\n${msg}`);
      }

      setSelectedAvailIds([]);
    } finally {
      setAdding(false);
    }
  };

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
            .then(() => ({ ok: true, vendor: v }))
            .catch((err) => ({ ok: false, vendor: v, error: err?.message || 'Failed' }))
        )
      );

      const successes = results.filter(r => (r.status === 'fulfilled' ? r.value.ok : r.value?.ok)).map(r => (r.status === 'fulfilled' ? r.value.vendor : r.value.vendor));
      const failures  = results.filter(r => !(r.status === 'fulfilled' ? r.value.ok : r.value?.ok));

      if (successes.length > 0) {
        const successIds = new Set(successes.map(v => v.vendId));
        const remainingRight = moveAvailableFileReceivedFrom.filter(v => !successIds.has(v.vendId));
        setMoveAvailableFileReceivedFrom(remainingRight);
        setAvailableFileReceivedFrom(prev => [...prev, ...successes]);
        setSelectedData?.(p => ({ ...p, vendorReceivedFrom: remainingRight }));
      }

      if (failures.length > 0) {
        const msg = failures.map(f => `${f.value.vendor?.vendId || '?'}: ${f.value.error || 'Failed'}`).join('\n');
        alert(`Some vendors could not be removed:\n${msg}`);
      }

      setSelectedSentIds([]);
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

                {/* Queue for mail (enabled on LEFT if selected; enabled on RIGHT only if selected AND all are queueForMail=true) */}
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
                    setActiveSide('right'); // make RIGHT active
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






import { Button, Typography } from '@mui/material';
import React, { useState, useEffect, useMemo } from 'react';
import { CCard, CCardBody } from '@coreui/react';

const PAGE_SIZE = 10;
const COLUMNS = 4;

const PreviewFilesReceivedFrom = ({ data }) => {
  const [page, setPage] = useState(0);

  // Normalize rows & compute a safe display name
  const rows = useMemo(() => {
    const arr = Array.isArray(data) ? data : [];
    return arr.map((it) => {
      const name =
        it.vendorName ??
        it.vendor?.name ??
        it.name ??
        it.vend_nm ??
        '';
      return { ...it, __displayName: (name || '').toString().trim() };
    });
  }, [data]);

  const pageCount = Math.ceil(rows.length / PAGE_SIZE) || 0;
  const pageData = rows.slice(page * PAGE_SIZE, (page + 1) * PAGE_SIZE);
  const hasData = rows.length > 0;

  useEffect(() => {
    if (hasData) console.info(JSON.stringify(rows, null, 2));
  }, [rows, hasData]);

  // keep page index in range if data changes
  useEffect(() => {
    if (page > 0 && page >= pageCount) setPage(0);
  }, [page, pageCount]);

  const cellStyle = {
    backgroundColor: 'white',
    minHeight: '25px',
    display: 'flex',
    alignItems: 'center',
    justifyContent: 'flex-start',
    fontSize: '0.78rem',
    fontWeight: 200,
    padding: '0 10px',
    borderBottom: '1px dotted #ddd',
  };

  const headerStyle = {
    ...cellStyle,
    fontWeight: 'bold',
    backgroundColor: '#f0f0f0',
    borderBottom: '1px dotted #ccc',
  };

  return (
    <>
      <CCard
        style={{
          height: '35px',
          marginBottom: '4px',
          marginTop: '2px',
          border: 'none',
          backgroundColor: '#f3f6f8',
          boxShadow: 'none',
          borderRadius: '4px',
        }}
      >
        <CCardBody
          className="d-flex align-items-center"
          style={{ padding: '0.25rem 0.5rem', height: '100%', backgroundColor: 'transparent' }}
        >
          <p style={{ margin: 0, fontSize: '0.78rem', fontWeight: 500 }}>General</p>
        </CCardBody>
      </CCard>

      <CCard style={{ height: '320px', marginBottom: '4px', marginTop: '15px' }}>
        <CCardBody className="d-flex align-items-center" style={{ padding: '0.25rem 0.5rem', height: '100%' }}>
          <div style={{ width: '100%', height: '100%' }}>
            <div style={{ display: 'flex', flexDirection: 'column', height: '100%' }}>
              {/* Grid Table */}
              <div
                style={{
                  flex: 1,
                  display: 'grid',
                  gridTemplateColumns: '1fr',
                  rowGap: '0px',
                  columnGap: '4px',
                  minHeight: '250px',
                  alignContent: 'start',
                }}
              >
                {/* Header Row */}
                <div style={headerStyle}>Name</div>

                {/* Data Rows */}
                {pageData.length > 0 ? (
                  pageData.map((item, index) => (
                    <div
                      key={`${item.vendorId ?? item.id ?? item.vendor?.id ?? index}-${index}`}
                      style={cellStyle}
                    >
                      {item.__displayName || <em style={{ color: '#6b7280' }}>(no name)</em>}
                    </div>
                  ))
                ) : (
                  <Typography
                    sx={{
                      gridColumn: `span ${COLUMNS}`,
                      fontSize: '0.75rem',
                      padding: '0 16px',
                      color: 'text.secondary',
                    }}
                  >
                    xxxx - xxxx
                  </Typography>
                )}
              </div>

              {/* Pagination */}
              <div
                style={{
                  marginTop: '16px',
                  display: 'flex',
                  justifyContent: 'space-between',
                  alignItems: 'center',
                }}
              >
                <Button
                  variant="text"
                  size="small"
                  sx={{ fontSize: '0.7rem', padding: '2px 8px', minWidth: 'unset', textTransform: 'none' }}
                  onClick={() => setPage((p) => Math.max(p - 1, 0))}
                  disabled={!hasData || page === 0}
                >
                  ◀ Previous
                </Button>

                <Typography fontSize="0.75rem">
                  Page {hasData ? page + 1 : 0} of {hasData ? pageCount : 0}
                </Typography>

                <Button
                  variant="text"
                  size="small"
                  sx={{ fontSize: '0.7rem', padding: '2px 8px', minWidth: 'unset', textTransform: 'none' }}
                  onClick={() => setPage((p) => Math.min(p + 1, Math.max(pageCount - 1, 0)))}
                  disabled={!hasData || page >= pageCount - 1}
                >
                  Next ▶
                </Button>
              </div>
            </div>
          </div>
        </CCardBody>
      </CCard>
    </>
  );
};

export default PreviewFilesReceivedFrom;
