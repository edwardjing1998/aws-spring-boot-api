import React, { useEffect, useMemo, useRef, useState } from 'react';
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
    vendorForReceivedFromTotal?: number | string | null; // ✅ may exist
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

  // ✅ allAvailable now becomes "CURRENT LEFT PAGE from server" (not full list)
  const [allAvailable, setAllAvailable] = useState<VendorItem[]>([]);

  const [right, setRight] = useState<VendorItem[]>([]);
  const [selLeftIds, setSelLeftIds] = useState<string[]>([]);
  const [selRightIds, setSelRightIds] = useState<string[]>([]);
  const [activeSide, setActiveSide] = useState<'left' | 'right'>('left');
  const [adding, setAdding] = useState(false);
  const [removing, setRemoving] = useState(false);

  // ✅ total from backend
  const vendorForReceivedFromTotal = toInt(selectedData?.vendorForReceivedFromTotal);

  // ✅ server-side paging state for LEFT
  const [leftPage, setLeftPage] = useState(0);
  const [leftLoading, setLeftLoading] = useState(false);

  // client-side paging for RIGHT stays same
  const [rightPage, setRightPage] = useState(0);

  // ✅ compute LEFT server page count from total
  const leftPageCount = useMemo(() => {
    const total = vendorForReceivedFromTotal ?? 0;
    return Math.max(1, Math.ceil(total / LEFT_PAGE_SIZE));
  }, [vendorForReceivedFromTotal]);

  // seed RIGHT from parent slice
  useEffect(() => {
    const seeded = (selectedData?.vendorReceivedFrom ?? [])
      .map(normalize)
      .filter((v: VendorItem) => v.vendId);
    setRight(seeded);
    setSelRightIds([]);
  }, [selectedData?.vendorReceivedFrom]);

  // ✅ reset LEFT paging when sysPrin changes
  useEffect(() => {
    setLeftPage(0);
    setSelLeftIds([]);
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

  // ✅ derive LEFT by subtracting RIGHT (from CURRENT server page only)
  const left = useMemo(() => {
    const rightIds = new Set(right.map((v) => v.vendId));
    return allAvailable.filter((v) => !rightIds.has(v.vendId));
  }, [allAvailable, right]);

  // ✅ leftPageData is simply the derived list from current server page
  // (no extra slicing; server already pages)
  const leftPageData = left;

  // -- RIGHT Pagination Logic (client-side) --
  const rightPageCount = Math.max(1, Math.ceil(right.length / RIGHT_PAGE_SIZE));

  useEffect(() => {
    if (rightPage > 0 && rightPage >= rightPageCount) {
      setRightPage(Math.max(0, rightPageCount - 1));
    }
  }, [right.length, rightPage, rightPageCount]);

  const rightPageData = useMemo(() => {
    return right.slice(rightPage * RIGHT_PAGE_SIZE, (rightPage + 1) * RIGHT_PAGE_SIZE);
  }, [right, rightPage]);

  // selections + tri-state
  const selectedLeft = useMemo(
    () => leftPageData.filter((v) => selLeftIds.includes(v.vendId)),
    [leftPageData, selLeftIds]
  );
  const selectedRight = useMemo(
    () => right.filter((v) => selRightIds.includes(v.vendId)),
    [right, selRightIds]
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
    (activeSide === 'left' ? selectedLeft.length === 0 : !rightEnableCondition);

  const checkboxRef = useRef<HTMLInputElement>(null);
  useEffect(() => {
    if (checkboxRef.current) checkboxRef.current.indeterminate = isIndeterminate;
  }, [isIndeterminate]);

  // LEFT-only toggle
  const onToggleQueue = (e: React.ChangeEvent<HTMLInputElement>) => {
    if (activeSide === 'right') return;
    const next = !!e.target.checked;
    // only update current server page rows
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

  // REMOVE (RIGHT -> LEFT after delete succeeds)
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

        // 2) add back to CURRENT LEFT PAGE only if it belongs there (optional)
        //    BUT since LEFT is server-paged, we can't guarantee which page it appears on.
        //    Best practice: just refetch current page so UI is consistent with server.
        //    ✅ This keeps your logic correct.
        // trigger refetch by setting same page (or call fetch inline)
        setLeftPage((p) => p);
      }

      setSelRightIds([]);
      setActiveSide('right');
    } finally {
      setRemoving(false);
    }
  };

  // ✅ LEFT Next/Prev (server paging)
  const canLeftPrev = leftPage > 0 && leftPageCount > 1;
  const canLeftNext = leftPageCount > 1 && leftPage < leftPageCount - 1;

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
                  disabled={!isEditable || adding || removing || leftLoading}
                >
                  {leftPageData.map((v) => (
                    <option key={v.vendId} value={v.vendId} style={optionStyle}>
                      {v.vendId} — {v.vendName} {v.queueForMail ? ' (Q)' : ''}
                    </option>
                  ))}
                </CFormSelect>

                {/* ✅ Left Side Pagination Controls (server-side) */}
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
                  disabled={!isEditable || selLeftIds.length === 0 || adding || removing || leftLoading}
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

                  {/* Right Side Pagination Controls (client-side) */}
                  <div style={{ display: 'flex', justifyContent: 'space-between', alignItems: 'center', marginTop: '4px' }}>
                    <Button
                      color="inherit"
                      variant="outlined"
                      size="small"
                      disabled={rightPage === 0 || rightPageCount <= 1}
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
                    </span>
                    <Button
                      color="inherit"
                      variant="outlined"
                      size="small"
                      disabled={rightPageCount <= 1 || rightPage >= rightPageCount - 1}
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



export async function fetchVendorsReceivedFrom(page: number, size: number, sysPrin: string): Promise<any[]> {
  const url = `${CLIENT_SYSPRIN_READER_API_BASE}/client/vendor-received-from/${encodeURIComponent(sysPrin)}?page=${encodeURIComponent(page)}&size=${encodeURIComponent(size)}`;
  const res = await fetch(url, { headers: { accept: '*/*' } });
  if (!res.ok) throw new Error(await parseError(res, 'Failed to load vendors'));
  const data = await res.json().catch(() => []);
  return Array.isArray(data) ? data : [];
}
