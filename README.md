import React, { useEffect, useMemo, useRef, useState } from 'react';
import { CCard, CCardBody, CCol, CRow, CFormSelect, CFormCheck } from '@coreui/react';
import { Button } from '@mui/material';

import {
  fetchVendorsForReceivedFrom,
  fetchVendorsReceivedFrom, // ✅ NEW: server-paged RIGHT list
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
    vendorForReceivedFromTotal?: number | string | null; // LEFT total
    vendorReceivedFromTotal?: number | string | null;    // RIGHT total
    [key: string]: any;
  } | null;
  onChangeVendorReceivedFrom?: (list: any[]) => void;
  isEditable: boolean;
  setSelectedData?: React.Dispatch<React.SetStateAction<any>>;
}

const LEFT_PAGE_SIZE = 10;
const RIGHT_PAGE_SIZE = 10;

const toInt = (v: any): number | undefined => {
  if (v === null || v === undefined) return undefined;
  const n = Number(String(v).trim());
  return Number.isFinite(n) ? n : undefined;
};

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
        v?.queue_for_mail_cd ??
        v?.queformail_cd;
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

  // Push updates to parent
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

  // --- state -----------------------------------------------------------------
  const sysPrin = String(selectedData?.sysPrin ?? '');

  // LEFT server page (available vendors)
  const [allAvailable, setAllAvailable] = useState<VendorItem[]>([]);
  const [leftPage, setLeftPage] = useState(0);
  const [leftLoading, setLeftLoading] = useState(false);

  // RIGHT server page (received-from vendors)
  const [right, setRight] = useState<VendorItem[]>([]);
  const [rightPage, setRightPage] = useState(0);
  const [rightLoading, setRightLoading] = useState(false);

  const [selLeftIds, setSelLeftIds] = useState<string[]>([]);
  const [selRightIds, setSelRightIds] = useState<string[]>([]);
  const [activeSide, setActiveSide] = useState<'left' | 'right'>('left');
  const [adding, setAdding] = useState(false);
  const [removing, setRemoving] = useState(false);

  // totals from backend
  const vendorForReceivedFromTotal = toInt(selectedData?.vendorForReceivedFromTotal); // LEFT total
  const vendorReceivedFromTotal = toInt(selectedData?.vendorReceivedFromTotal);       // RIGHT total

  // compute server page counts
  const leftPageCount = useMemo(() => {
    const total = vendorForReceivedFromTotal ?? 0;
    return Math.max(1, Math.ceil(total / LEFT_PAGE_SIZE));
  }, [vendorForReceivedFromTotal]);

  const rightPageCount = useMemo(() => {
    const total = vendorReceivedFromTotal ?? 0;
    return Math.max(1, Math.ceil(total / RIGHT_PAGE_SIZE));
  }, [vendorReceivedFromTotal]);

  // reset paging when sysPrin changes
  useEffect(() => {
    setLeftPage(0);
    setRightPage(0);
    setSelLeftIds([]);
    setSelRightIds([]);
  }, [sysPrin]);

  // ✅ load LEFT page from server
  useEffect(() => {
    if (!sysPrin) {
      setAllAvailable([]);
      return;
    }

    let alive = true;
    (async () => {
      setLeftLoading(true);
      try {
        const raw = await fetchVendorsForReceivedFrom(leftPage, LEFT_PAGE_SIZE, sysPrin, 'O');
        if (!alive) return;
        setAllAvailable((Array.isArray(raw) ? raw : []).map(normalize));
      } catch (err) {
        console.error('Failed to load vendors (O):', err);
        if (!alive) return;
        setAllAvailable([]);
      } finally {
        if (alive) setLeftLoading(false);
      }
    })();

    return () => {
      alive = false;
    };
  }, [sysPrin, leftPage]);

  // ✅ load RIGHT page from server (vendorReceivedFrom)
  useEffect(() => {
    if (!sysPrin) {
      setRight([]);
      return;
    }

    let alive = true;
    (async () => {
      setRightLoading(true);
      try {
        const raw = await fetchVendorsReceivedFrom(rightPage, RIGHT_PAGE_SIZE, sysPrin);
        if (!alive) return;
        setRight((Array.isArray(raw) ? raw : []).map(normalize).filter((v) => v.vendId));
      } catch (err) {
        console.error('Failed to load vendorsReceivedFrom:', err);
        if (!alive) return;
        setRight([]);
      } finally {
        if (alive) setRightLoading(false);
      }
    })();

    return () => {
      alive = false;
    };
  }, [sysPrin, rightPage]);

  // derive LEFT by subtracting RIGHT (from CURRENT server page only)
  const left = useMemo(() => {
    const rightIds = new Set(right.map((v) => v.vendId));
    return allAvailable.filter((v) => !rightIds.has(v.vendId));
  }, [allAvailable, right]);

  // leftPageData is derived list from current LEFT server page
  const leftPageData = left;

  // rightPageData is simply the current RIGHT server page
  const rightPageData = right;

  // selections + tri-state
  const selectedLeft = useMemo(
    () => leftPageData.filter((v) => selLeftIds.includes(v.vendId)),
    [leftPageData, selLeftIds]
  );
  const selectedRight = useMemo(
    () => rightPageData.filter((v) => selRightIds.includes(v.vendId)),
    [rightPageData, selRightIds]
  );

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
    !isEditable ||
    adding ||
    removing ||
    leftLoading ||
    rightLoading ||
    (activeSide === 'left' ? selectedLeft.length === 0 : !rightEnableCondition);

  const checkboxRef = useRef<HTMLInputElement>(null);
  useEffect(() => {
    if (checkboxRef.current) checkboxRef.current.indeterminate = isIndeterminate;
  }, [isIndeterminate]);

  // LEFT-only toggle (only affects current LEFT page rows)
  const onToggleQueue = (e: React.ChangeEvent<HTMLInputElement>) => {
    if (activeSide === 'right') return;
    const next = !!e.target.checked;
    setAllAvailable((prev) => prev.map((v) => (selLeftIds.includes(v.vendId) ? { ...v, queueForMail: next } : v)));
  };

  // ADD (LEFT -> RIGHT) via service
  const handleAdd = async () => {
    if (!isEditable || selLeftIds.length === 0 || !sysPrin) return;
    setAdding(true);
    try {
      const toAdd = leftPageData.filter((v) => selLeftIds.includes(v.vendId));

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
        // ✅ RIGHT is server-paged now: best practice is refetch current RIGHT page
        // to stay consistent with server. Still push to parent so preview has latest.
        pushRightToParent([...right, ...successes.filter((s) => !new Set(right.map((x) => x.vendId)).has(s.vendId))]);

        // trigger refetch (no-op set)
        setRightPage((p) => p);
        // also refresh LEFT because items may disappear from LEFT after move
        setLeftPage((p) => p);
      }

      setSelLeftIds([]);
      setActiveSide('left');
    } finally {
      setAdding(false);
    }
  };

  // REMOVE (RIGHT -> LEFT after delete succeeds)
  const handleRemove = async () => {
    if (!isEditable || selRightIds.length === 0 || !sysPrin) return;
    setRemoving(true);
    try {
      const toRemove = rightPageData.filter((v) => selRightIds.includes(v.vendId));

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
        // ✅ RIGHT is server-paged: refetch current right page
        setRightPage((p) => p);
        // ✅ LEFT is server-paged too: refetch current left page
        setLeftPage((p) => p);

        // also update parent best-effort (remove from current page items)
        const successIds = new Set(successes.map((v) => v.vendId));
        const remainingRight = right.filter((v) => !successIds.has(v.vendId));
        pushRightToParent(remainingRight);
      }

      setSelRightIds([]);
      setActiveSide('right');
    } finally {
      setRemoving(false);
    }
  };

  // LEFT Next/Prev (server paging)
  const canLeftPrev = leftPage > 0 && leftPageCount > 1;
  const canLeftNext = leftPageCount > 1 && leftPage < leftPageCount - 1;

  // ✅ RIGHT Next/Prev (server paging)
  const canRightPrev = rightPage > 0 && rightPageCount > 1;
  const canRightNext = rightPageCount > 1 && rightPage < rightPageCount - 1;

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
                  disabled={!isEditable || adding || removing || leftLoading || rightLoading}
                >
                  {leftPageData.map((v) => (
                    <option key={v.vendId} value={v.vendId} style={optionStyle}>
                      {v.vendId} — {v.vendName} {v.queueForMail ? ' (Q)' : ''}
                    </option>
                  ))}
                </CFormSelect>

                {/* Left Side Pagination Controls (server-side) */}
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
                    disabled={!canLeftPrev || leftLoading}
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
                    {leftLoading ? ' (Loading...)' : ''}
                  </span>

                  <Button
                    color="inherit"
                    variant="outlined"
                    size="small"
                    disabled={!canLeftNext || leftLoading}
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
              <CCol
                md={2}
                className="d-flex flex-column align-items-center justify-content-center"
                style={{ minHeight: 200, gap: 24 }}
              >
                <Button
                  color="success"
                  variant="outlined"
                  size="small"
                  style={buttonStyle}
                  onClick={handleAdd}
                  disabled={
                    !isEditable ||
                    selLeftIds.length === 0 ||
                    adding ||
                    removing ||
                    leftLoading ||
                    rightLoading
                  }
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
                  disabled={
                    !isEditable ||
                    selRightIds.length === 0 ||
                    adding ||
                    removing ||
                    leftLoading ||
                    rightLoading
                  }
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
                    disabled={!isEditable || adding || removing || rightLoading || leftLoading}
                  >
                    {rightPageData.map((v) => (
                      <option key={v.vendId} value={v.vendId} style={optionStyle}>
                        {v.vendId} — {v.vendName} {v.queueForMail ? ' (Q)' : ''}
                      </option>
                    ))}
                  </CFormSelect>

                  {/* ✅ Right Side Pagination Controls (server-side) */}
                  <div style={{ display: 'flex', justifyContent: 'space-between', alignItems: 'center', marginTop: '4px' }}>
                    <Button
                      color="inherit"
                      variant="outlined"
                      size="small"
                      disabled={!canRightPrev || rightLoading}
                      onClick={() => setRightPage((p) => Math.max(0, p - 1))}
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
                      Page {rightPage + 1} of {rightPageCount}
                      {rightLoading ? ' (Loading...)' : ''}
                    </span>

                    <Button
                      color="inherit"
                      variant="outlined"
                      size="small"
                      disabled={!canRightNext || rightLoading}
                      onClick={() => setRightPage((p) => Math.min(rightPageCount - 1, p + 1))}
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
