import React, { useEffect, useMemo, useRef, useState, useCallback } from 'react';
import { CCard, CCardBody, CCol, CRow, CFormSelect, CFormCheck } from '@coreui/react';
import { Button } from '@mui/material';

import {
  fetchVendorsForReceivedFrom,
  addReceivedFrom,
  deleteReceivedFrom,
} from './EditFileReceivedFrom.service';

// --- Interfaces ---

export interface VendorItem {
  vendId: string;
  vendName: string;
  queueForMail: boolean;
  [key: string]: any;
}

interface EditFileReceivedFromProps {
  selectedData: {
    sysPrin?: string;
    vendorReceivedFrom?: any[];
    vendorForReceivedFromTotal?: number | string; // <-- total available from backend (optional)
    [key: string]: any;
  } | null;
  onChangeVendorReceivedFrom?: (list: any[]) => void;
  isEditable: boolean;
  setSelectedData?: React.Dispatch<React.SetStateAction<any>>;
}

const PAGE_SIZE = 10;

const EditFileReceivedFrom: React.FC<EditFileReceivedFromProps> = ({
  selectedData,
  onChangeVendorReceivedFrom,
  isEditable,
  setSelectedData,
}) => {
  // --- helpers ---------------------------------------------------------------
  const normalize = (v: any = {}): VendorItem => ({
    vendId: String(v?.vendId ?? v?.vendorId ?? v?.vendor?.vendId ?? v?.vendor?.id ?? ''),
    vendName:
      v?.vendName ??
      v?.vendorName ??
      v?.vendor?.vendNm ??
      v?.vendor?.name ??
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

  const toParentShape = (list: VendorItem[]) =>
    list.map((v) => ({
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

  const pushRightToParent = (rightList: VendorItem[]) => {
    const canonical = toParentShape(rightList);
    if (typeof onChangeVendorReceivedFrom === 'function') {
      onChangeVendorReceivedFrom(canonical);
      return;
    }
    if (typeof setSelectedData === 'function') {
      setSelectedData((prev: any) => ({ ...(prev ?? {}), vendorReceivedFrom: canonical }));
    }
  };

  const sysPrin = String(selectedData?.sysPrin ?? '');

  // ------------------- state -------------------
  const [allAvailable, setAllAvailable] = useState<VendorItem[]>([]);
  const [right, setRight] = useState<VendorItem[]>([]);
  const [selLeftIds, setSelLeftIds] = useState<string[]>([]);
  const [selRightIds, setSelRightIds] = useState<string[]>([]);
  const [activeSide, setActiveSide] = useState<'left' | 'right'>('left');
  const [adding, setAdding] = useState(false);
  const [removing, setRemoving] = useState(false);

  // ✅ total available (from selectedData); used to enable server paging
  const vendorForReceivedFromTotalRaw = selectedData?.vendorForReceivedFromTotal;
  const vendorForReceivedFromTotal =
    vendorForReceivedFromTotalRaw === null || vendorForReceivedFromTotalRaw === undefined
      ? undefined
      : Number(vendorForReceivedFromTotalRaw);

  // ✅ server-side paging state for "available vendors (O)"
  const [availPage, setAvailPage] = useState(0); // 0-based
  const availPageCount =
    vendorForReceivedFromTotal && vendorForReceivedFromTotal > 0
      ? Math.ceil(vendorForReceivedFromTotal / PAGE_SIZE)
      : 1;

  // ✅ local paging state for RIGHT box (unchanged)
  const [rightPage, setRightPage] = useState(0);
  const RIGHT_PAGE_SIZE = 10;

  // seed RIGHT from parent slice
  useEffect(() => {
    const seeded = (selectedData?.vendorReceivedFrom ?? [])
      .map(normalize)
      .filter((v: VendorItem) => v.vendId);
    setRight(seeded);
    setSelRightIds([]);
    setRightPage(0);
  }, [selectedData?.vendorReceivedFrom]);

  // ✅ load a page of available vendors from backend
  const loadAvailable = useCallback(
    async (page: number) => {
      if (!sysPrin) {
        setAllAvailable([]);
        return;
      }
      try {
        const raw = await fetchVendorsForReceivedFrom(page, PAGE_SIZE, sysPrin, 'O');
        setAllAvailable((Array.isArray(raw) ? raw : []).map(normalize));
      } catch (err) {
        console.error('Failed to load vendors (O):', err);
      }
    },
    [sysPrin]
  );

  // initial load or when sysPrin changes
  useEffect(() => {
    setAvailPage(0);
    loadAvailable(0);
  }, [sysPrin, loadAvailable]);

  // derive LEFT by subtracting RIGHT (LEFT is always current server page minus rightIds)
  const left = useMemo(() => {
    const rightIds = new Set(right.map((v) => v.vendId));
    return allAvailable.filter((v) => !rightIds.has(v.vendId));
  }, [allAvailable, right]);

  // client-side paging for LEFT within the server page (optional).
  // Since server already gives 10, this is usually just 1 page.
  const [leftPage, setLeftPage] = useState(0);
  const LEFT_PAGE_SIZE = 10;

  useEffect(() => {
    setLeftPage(0);
  }, [allAvailable]);

  const leftPageCount = Math.max(1, Math.ceil(left.length / LEFT_PAGE_SIZE));
  const leftPageData = useMemo(() => left.slice(leftPage * LEFT_PAGE_SIZE, (leftPage + 1) * LEFT_PAGE_SIZE), [left, leftPage]);

  // RIGHT local paging
  const rightPageCount = Math.max(1, Math.ceil(right.length / RIGHT_PAGE_SIZE));
  useEffect(() => {
    if (rightPage > 0 && rightPage >= rightPageCount) {
      setRightPage(Math.max(0, rightPageCount - 1));
    }
  }, [right.length, rightPage, rightPageCount]);

  const rightPageData = useMemo(() => right.slice(rightPage * RIGHT_PAGE_SIZE, (rightPage + 1) * RIGHT_PAGE_SIZE), [right, rightPage]);

  // selections + tri-state
  const selectedLeft = useMemo(() => left.filter((v) => selLeftIds.includes(v.vendId)), [left, selLeftIds]);
  const selectedRight = useMemo(() => right.filter((v) => selRightIds.includes(v.vendId)), [right, selRightIds]);

  const leftAllTrue = selectedLeft.length > 0 && selectedLeft.every((v) => v.queueForMail);
  const leftAllFalse = selectedLeft.length > 0 && selectedLeft.every((v) => !v.queueForMail);
  const leftInd = selectedLeft.length > 1 && !leftAllTrue && !leftAllFalse;

  const rightAllTrue = selectedRight.length > 0 && selectedRight.every((v) => v.queueForMail);
  const rightAllFalse = selectedRight.length > 0 && selectedRight.every((v) => !v.queueForMail);
  const rightInd = selectedRight.length > 1 && !rightAllTrue && !rightAllFalse;

  const isIndeterminate = activeSide === 'left' ? leftInd : rightInd;
  const currentChecked =
    activeSide === 'left'
      ? selectedLeft.length === 0
        ? false
        : leftAllTrue
      : selectedRight.length === 0
      ? false
      : rightAllTrue;

  const rightEnableCondition = selectedRight.length > 0 && rightAllTrue;
  const checkboxDisabled =
    !isEditable || adding || removing || (activeSide === 'left' ? selectedLeft.length === 0 : !rightEnableCondition);

  const checkboxRef = useRef<HTMLInputElement>(null);
  useEffect(() => {
    if (checkboxRef.current) checkboxRef.current.indeterminate = isIndeterminate;
  }, [isIndeterminate]);

  // LEFT-only toggle
  const onToggleQueue = (e: React.ChangeEvent<HTMLInputElement>) => {
    if (activeSide === 'right') return;
    const next = !!e.target.checked;
    setAllAvailable((prev) => prev.map((v) => (selLeftIds.includes(v.vendId) ? { ...v, queueForMail: next } : v)));
  };

  // ADD (LEFT -> RIGHT)
  const handleAdd = async () => {
    if (!isEditable || selLeftIds.length === 0 || !sysPrin) return;
    setAdding(true);
    try {
      const toAdd = left.filter((v) => selLeftIds.includes(v.vendId));

      const results = await Promise.allSettled(
        toAdd.map((v) => addReceivedFrom(sysPrin, v.vendId, v.queueForMail).then(() => v))
      );

      const successes = results
        .filter((r) => r.status === 'fulfilled')
        .map((r) => (r as PromiseFulfilledResult<VendorItem>).value);

      const failed = results.filter((r) => r.status === 'rejected');
      if (failed.length) {
        alert(
          `Some failed:\n${failed
            .map((f: any, i) => `#${i + 1}: ${f.reason?.message ?? String(f.reason)}`)
            .join('\n')}`
        );
      }

      if (successes.length) {
        setRight((prev) => {
          const ids = new Set(prev.map((x) => x.vendId));
          const merged = [...prev, ...successes.filter((s) => !ids.has(s.vendId))];
          pushRightToParent(merged);
          return merged;
        });
      }

      setSelLeftIds([]);
      setActiveSide('left');
    } finally {
      setAdding(false);
    }
  };

  // REMOVE (RIGHT -> LEFT)
  const handleRemove = async () => {
    if (!isEditable || selRightIds.length === 0 || !sysPrin) return;
    setRemoving(true);
    try {
      const toRemove = right.filter((v) => selRightIds.includes(v.vendId));

      const results = await Promise.allSettled(
        toRemove.map((v) => deleteReceivedFrom(sysPrin, v.vendId, v.queueForMail).then(() => v))
      );

      const successes = results
        .filter((r) => r.status === 'fulfilled')
        .map((r) => (r as PromiseFulfilledResult<VendorItem>).value);

      const failed = results.filter((r) => r.status === 'rejected');
      if (failed.length) {
        alert(
          `Some failed:\n${failed
            .map((f: any, i) => `#${i + 1}: ${f.reason?.message ?? String(f.reason)}`)
            .join('\n')}`
        );
      }

      if (successes.length) {
        const successIds = new Set(successes.map((v) => v.vendId));

        // 1) remove from RIGHT
        const remainingRight = right.filter((v) => !successIds.has(v.vendId));
        setRight(remainingRight);
        pushRightToParent(remainingRight);

        // 2) refresh current available page from backend (so LEFT reflects true server state)
        await loadAvailable(availPage);
      }

      setSelRightIds([]);
      setActiveSide('right');
    } finally {
      setRemoving(false);
    }
  };

  // ✅ RIGHT "Next" now pages AVAILABLE vendors if total > 10
  const rightCanPageServer = (vendorForReceivedFromTotal ?? 0) > PAGE_SIZE;

  const handleAvailNext = async () => {
    if (!rightCanPageServer) return;
    const nextPage = Math.min(availPage + 1, availPageCount - 1);
    if (nextPage === availPage) return;
    setAvailPage(nextPage);
    await loadAvailable(nextPage);
    setSelLeftIds([]);
    setActiveSide('left');
  };

  const handleAvailPrev = async () => {
    if (!rightCanPageServer) return;
    const prevPage = Math.max(availPage - 1, 0);
    if (prevPage === availPage) return;
    setAvailPage(prevPage);
    await loadAvailable(prevPage);
    setSelLeftIds([]);
    setActiveSide('left');
  };

  // styles
  const selectStyle: React.CSSProperties = {
    height: '350px',
    fontSize: '0.78rem',
    width: '100%',
    maxWidth: '350px',
    paddingLeft: '16px',
    scrollbarWidth: 'none',
    msOverflowStyle: 'none',
  };
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
                  multiple
                  // @ts-ignore
                  size={10 as any}
                  style={selectStyle}
                  value={selLeftIds}
                  onChange={(e) => {
                    const newlySelected = Array.from(e.target.selectedOptions, (o) => o.value);
                    const currentVisibleIds = new Set(leftPageData.map((v) => v.vendId));
                    setSelLeftIds((prev) => {
                      const otherPageSelections = prev.filter((id) => !currentVisibleIds.has(id));
                      return [...otherPageSelections, ...newlySelected];
                    });
                    setActiveSide('left');
                  }}
                  onClick={() => setActiveSide('left')}
                  disabled={!isEditable || adding || removing}
                >
                  {leftPageData.map((v) => (
                    <option key={v.vendId} value={v.vendId} style={optionStyle}>
                      {v.vendId} — {v.vendName} {v.queueForMail ? ' (Q)' : ''}
                    </option>
                  ))}
                </CFormSelect>

                {/* Left Side Pagination Controls (inside one server page; usually disabled) */}
                <div
                  style={{
                    display: 'flex',
                    justifyContent: 'space-between',
                    alignItems: 'center',
                    marginTop: '4px',
                    maxWidth: '350px',
                  }}
                >
                  <Button
                    color="inherit"
                    variant="outlined"
                    size="small"
                    disabled={leftPage === 0 || leftPageCount <= 1}
                    onClick={() => setLeftPage((p) => Math.max(0, p - 1))}
                    sx={{
                      fontSize: '0.7rem',
                      padding: '2px 6px',
                      minWidth: 'auto',
                      textTransform: 'none',
                      color: '#666',
                      borderColor: '#ccc',
                    }}
                  >
                    Prev
                  </Button>
                  <span style={{ fontSize: '0.75rem', color: '#666' }}>
                    Page {leftPage + 1} of {leftPageCount}
                  </span>
                  <Button
                    color="inherit"
                    variant="outlined"
                    size="small"
                    disabled={leftPageCount <= 1 || leftPage >= leftPageCount - 1}
                    onClick={() => setLeftPage((p) => Math.min(leftPageCount - 1, p + 1))}
                    sx={{
                      fontSize: '0.7rem',
                      padding: '2px 6px',
                      minWidth: 'auto',
                      textTransform: 'none',
                      color: '#666',
                      borderColor: '#ccc',
                    }}
                  >
                    Next
                  </Button>
                </div>
              </CCol>

              {/* MIDDLE */}
              <CCol md={2} className="d-flex flex-column align-items-center justify-content-center" style={{ minHeight: 200, gap: 24 }}>
                <Button
                  color="success"
                  variant="outlined"
                  size="small"
                  style={buttonStyle}
                  onClick={handleAdd}
                  disabled={!isEditable || selLeftIds.length === 0 || adding || removing}
                >
                  {adding ? 'Adding…' : 'Add ⬇️'}
                </Button>

                <div style={{ display: 'flex', alignItems: 'center', gap: 8, fontSize: '0.72rem', lineHeight: 1.1 }}>
                  <CFormCheck
                    id="queueForMail"
                    checked={currentChecked}
                    onChange={onToggleQueue}
                    disabled={checkboxDisabled}
                    ref={checkboxRef}
                    label=""
                  />
                  <label htmlFor="queueForMail" style={{ margin: 0, cursor: checkboxDisabled ? 'default' : 'pointer' }}>
                    Queue for mail
                  </label>
                </div>

                <Button
                  color="error"
                  variant="outlined"
                  size="small"
                  style={buttonStyle}
                  onClick={handleRemove}
                  disabled={!isEditable || selRightIds.length === 0 || adding || removing}
                >
                  {removing ? 'Removing…' : '⬆️ Remove'}
                </Button>
              </CCol>

              {/* RIGHT */}
              <CCol md={5} className="d-flex justify-content-end">
                <div style={{ width: '100%', maxWidth: '350px' }}>
                  <CFormSelect
                    multiple
                    // @ts-ignore
                    size={10 as any}
                    style={selectStyle}
                    value={selRightIds}
                    onChange={(e) => {
                      const newlySelected = Array.from(e.target.selectedOptions, (o) => o.value);
                      const currentVisibleIds = new Set(rightPageData.map((v) => v.vendId));
                      setSelRightIds((prev) => {
                        const otherPageSelections = prev.filter((id) => !currentVisibleIds.has(id));
                        return [...otherPageSelections, ...newlySelected];
                      });
                      setActiveSide('right');
                    }}
                    onClick={() => setActiveSide('right')}
                    disabled={!isEditable || adding || removing}
                  >
                    {rightPageData.map((v) => (
                      <option key={v.vendId} value={v.vendId} style={optionStyle}>
                        {v.vendId} — {v.vendName} {v.queueForMail ? ' (Q)' : ''}
                      </option>
                    ))}
                  </CFormSelect>

                  {/* ✅ Right Side Pagination Controls:
                      - Prev/Next controls AVAILABLE vendors pages (server-side)
                      - Enabled when vendorForReceivedFromTotal > 10
                   */}
                  <div style={{ display: 'flex', justifyContent: 'space-between', alignItems: 'center', marginTop: '4px' }}>
                    <Button
                      color="inherit"
                      variant="outlined"
                      size="small"
                      disabled={!rightCanPageServer || availPage === 0 || adding || removing}
                      onClick={handleAvailPrev}
                      sx={{
                        fontSize: '0.7rem',
                        padding: '2px 6px',
                        minWidth: 'auto',
                        textTransform: 'none',
                        color: '#666',
                        borderColor: '#ccc',
                      }}
                    >
                      Prev
                    </Button>

                    <span style={{ fontSize: '0.75rem', color: '#666' }}>
                      Avail Page {rightCanPageServer ? availPage + 1 : 1} of {rightCanPageServer ? availPageCount : 1}
                    </span>

                    <Button
                      color="inherit"
                      variant="outlined"
                      size="small"
                      disabled={!rightCanPageServer || availPage >= availPageCount - 1 || adding || removing}
                      onClick={handleAvailNext}
                      sx={{
                        fontSize: '0.7rem',
                        padding: '2px 6px',
                        minWidth: 'auto',
                        textTransform: 'none',
                        color: '#666',
                        borderColor: '#ccc',
                      }}
                    >
                      Next
                    </Button>
                  </div>
                </div>
              </CCol>
            </CRow>
          </CCardBody>
        </CCard>
      </CCol>
    </CRow>
  );
};

export default EditFileReceivedFrom;
