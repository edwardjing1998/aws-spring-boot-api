import React, { useEffect, useMemo, useRef, useState } from 'react';
import {
  CCard, CCardBody, CCol, CRow, CFormSelect, CFormCheck,
} from '@coreui/react';
import { Button } from '@mui/material';

// --- Interfaces ---

export interface VendorItem {
  vendId: string;
  vendName: string;
  queueForMail: boolean;
  [key: string]: any;
}

interface EditFileSentToProps {
  selectedData: {
    sysPrin?: string;
    vendorSentTo?: any[];
    [key: string]: any;
  } | null;
  setSelectedData?: React.Dispatch<React.SetStateAction<any>>;
  isEditable: boolean;
  onChangeVendorSentTo?: (list: any[]) => void;
}

const EditFileSentTo: React.FC<EditFileSentToProps> = ({
  selectedData,
  setSelectedData,        // optional fallback
  isEditable,
  onChangeVendorSentTo,   // focused updater from parent
}) => {
  // ---------- helpers ----------
  const normalize = (v: any = {}): VendorItem => ({
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

  const toParentShape = (list: VendorItem[]) =>
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
  const pushRightToParent = (rightList: VendorItem[]) => {
    const canonical = toParentShape(rightList);
    if (typeof onChangeVendorSentTo === 'function') {
      onChangeVendorSentTo(canonical);
      return;
    }
    if (typeof setSelectedData === 'function') {
      setSelectedData((prev: any) => ({ ...(prev ?? {}), vendorSentTo: canonical }));
    }
  };

  // ---------- state ----------
  const sysPrin = String(selectedData?.sysPrin ?? '');
  const [allAvailable, setAllAvailable] = useState<VendorItem[]>([]); // master pool (fileIo=I)
  const [right, setRight] = useState<VendorItem[]>([]);               // assigned list ("sent-to")

  const [selLeftIds, setSelLeftIds] = useState<string[]>([]);
  const [selRightIds, setSelRightIds] = useState<string[]>([]);
  const [activeSide, setActiveSide] = useState<'left' | 'right'>('left');

  const [saving, setSaving] = useState(false);
  const [removing, setRemoving] = useState(false);

  // Pagination for LEFT list
  const [leftPage, setLeftPage] = useState(0);
  const LEFT_PAGE_SIZE = 10;

  // Pagination for RIGHT list
  const [rightPage, setRightPage] = useState(0);
  const RIGHT_PAGE_SIZE = 10;

  // ---------- seed RIGHT from parent slice (DEPEND ON THE SLICE) ------------
  useEffect(() => {
    const seeded = (selectedData?.vendorSentTo ?? []).map(normalize).filter((v: VendorItem) => v.vendId);
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

  // -- LEFT Pagination Logic --
  const leftPageCount = Math.max(1, Math.ceil(left.length / LEFT_PAGE_SIZE));
  
  useEffect(() => {
    if (leftPage > 0 && leftPage >= leftPageCount) {
        setLeftPage(Math.max(0, leftPageCount - 1));
    }
  }, [left.length, leftPage, leftPageCount]);

  const leftPageData = useMemo(() => {
      return left.slice(leftPage * LEFT_PAGE_SIZE, (leftPage + 1) * LEFT_PAGE_SIZE);
  }, [left, leftPage]);

  // -- RIGHT Pagination Logic --
  const rightPageCount = Math.max(1, Math.ceil(right.length / RIGHT_PAGE_SIZE));

  useEffect(() => {
    if (rightPage > 0 && rightPage >= rightPageCount) {
        setRightPage(Math.max(0, rightPageCount - 1));
    }
  }, [right.length, rightPage, rightPageCount]);

  const rightPageData = useMemo(() => {
      return right.slice(rightPage * RIGHT_PAGE_SIZE, (rightPage + 1) * RIGHT_PAGE_SIZE);
  }, [right, rightPage]);


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

  const rightEnableCondition = selectedRight.length > 0 && rightAllTrue;
  const checkboxDisabled =
    !isEditable || saving || removing ||
    (activeSide === 'left' ? selectedLeft.length === 0 : !rightEnableCondition);

  const checkboxRef = useRef<HTMLInputElement>(null);
  useEffect(() => {
    if (checkboxRef.current) checkboxRef.current.indeterminate = isIndeterminate;
  }, [isIndeterminate]);

  // ---------- checkbox toggle (LEFT only) ----------
  const onToggleQueue = (e: React.ChangeEvent<HTMLInputElement>) => {
    if (activeSide === 'right') return;
    const next = !!e.target.checked;
    setAllAvailable(prev =>
      prev.map(v => (selLeftIds.includes(v.vendId) ? { ...v, queueForMail: next } : v))
    );
  };

  // ---------- API helpers ----------
  const postAdd = async (sysPrin: string, vendorId: string, queForMail: boolean) => {
    const url = `http://localhost:8089/client-sysprin-writer/api/sysprins/${encodeURIComponent(sysPrin)}/sent-to/create`;
    const res = await fetch(url, {
      method: 'POST',
      headers: { accept: '*/*', 'Content-Type': 'application/json' },
      body: JSON.stringify({ sysPrin, vendorId, queForMail }),
    });
    if (!res.ok) throw new Error(await res.text());
    return true;
  };

  const delRemove = async (sysPrin: string, vendorId: string, queForMail: boolean) => {
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
      const successes = results.filter(r => r.status === 'fulfilled').map(r => (r as PromiseFulfilledResult<VendorItem>).value);
      const failed = results.filter(r => r.status === 'rejected');
      if (failed.length) {
        alert(`Some failed:\n${failed.map((f: any, i) => `#${i+1}: ${f.reason}`).join('\n')}`);
      }

      if (successes.length) {
        setRight(prev => {
          const ids = new Set(prev.map(x => x.vendId));
          const merged = [...prev, ...successes.filter(s => !ids.has(s.vendId))];
          pushRightToParent(merged); // sync parent + clientList
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
      const successes = results.filter(r => r.status === 'fulfilled').map(r => (r as PromiseFulfilledResult<VendorItem>).value);
      const failed = results.filter(r => r.status === 'rejected');
      if (failed.length) {
        alert(`Some failed:\n${failed.map((f: any, i) => `#${i+1}: ${f.reason}`).join('\n')}`);
      }

      if (successes.length) {
        const successIds = new Set(successes.map(v => v.vendId));
        const remainingRight = right.filter(v => !successIds.has(v.vendId));
        setRight(remainingRight);
        pushRightToParent(remainingRight); // sync parent + clientList
      }

      setSelRightIds([]);
      setActiveSide('right');
    } finally {
      setRemoving(false);
    }
  };

  // ---------- styles ----------
  const selectStyle: React.CSSProperties = {
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
                  // @ts-ignore
                  size={10 as any}
                  style={selectStyle}
                  value={selLeftIds}
                  onChange={(e) => {
                    // Logic to update selection while preserving selections from OTHER pages
                    const newlySelected = Array.from(e.target.selectedOptions, o => o.value);
                    const currentVisibleIds = new Set(leftPageData.map(v => v.vendId));
                    
                    setSelLeftIds(prev => {
                        // keep IDs that are NOT on the current page
                        const otherPageSelections = prev.filter(id => !currentVisibleIds.has(id));
                        return [...otherPageSelections, ...newlySelected];
                    });
                    setActiveSide('left');
                  }}
                  onClick={() => setActiveSide('left')}
                  disabled={!isEditable || saving || removing}
                >
                  {/* Map over paged data instead of full 'left' list */}
                  {leftPageData.map(v => (
                    <option key={v.vendId} value={v.vendId} style={optionStyle}>
                      {v.vendId} — {v.vendName} {v.queueForMail ? ' (Q)' : ''}
                    </option>
                  ))}
                </CFormSelect>

                {/* Left Side Pagination Controls */}
                <div style={{ display: 'flex', justifyContent: 'space-between', alignItems: 'center', marginTop: '4px', maxWidth: '350px' }}>
                    <Button
                        color="inherit"
                        variant="outlined"
                        size="small"
                        disabled={leftPage === 0}
                        onClick={() => setLeftPage(p => Math.max(0, p - 1))}
                        sx={{ fontSize: '0.7rem', padding: '2px 6px', minWidth: 'auto', textTransform: 'none', color: '#666', borderColor: '#ccc' }}
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
                        disabled={leftPage >= leftPageCount - 1}
                        onClick={() => setLeftPage(p => Math.min(leftPageCount - 1, p + 1))}
                        sx={{ fontSize: '0.7rem', padding: '2px 6px', minWidth: 'auto', textTransform: 'none', color: '#666', borderColor: '#ccc' }}
                    >
                        Next
                    </Button>
                </div>
              </CCol>

              {/* MIDDLE: Save / Queue / Remove */}
              <CCol
                md={2}
                className="d-flex flex-column align-items-center justify-content-center order-md-2"
                style={{ minHeight: '200px', gap: '24px' }}
              >
                <Button
                  color="success"
                  variant="outlined"
                  size="small"
                  style={buttonStyle}
                  onClick={handleSave}
                  disabled={!isEditable || selLeftIds.length === 0 || saving || removing}
                >
                  {saving ? 'Saving…' : 'Save ⬇️'}
                </Button>

                <div style={{ display: 'flex', alignItems: 'center', gap: 8, fontSize: '0.72rem', lineHeight: 1.1 }}>
                  <CFormCheck
                    id="queueForMailSentTo"
                    checked={currentChecked}
                    onChange={onToggleQueue}
                    disabled={checkboxDisabled}
                    ref={checkboxRef}
                    label=""
                  />
                  <label htmlFor="queueForMailSentTo" style={{ margin: 0, cursor: checkboxDisabled ? 'default' : 'pointer' }}>
                    Queue for mail
                  </label>
                </div>

                <Button
                  color="error"
                  variant="outlined"
                  size="small"
                  style={buttonStyle}
                  onClick={handleRemove}
                  disabled={!isEditable || selRightIds.length === 0 || saving || removing}
                >
                  {removing ? 'Removing…' : '⬆️ Remove'}
                </Button>
              </CCol>

              {/* RIGHT: assigned (seeded from parent, synced on save/remove) */}
              <CCol md={5} className="order-md-3 d-flex justify-content-end">
                <div style={{ width: '100%', maxWidth: '350px' }}>
                    <CFormSelect
                      multiple
                      // @ts-ignore
                      size={10 as any}
                      style={selectStyle}
                      value={selRightIds}
                      onChange={(e) => {
                        // Logic to update selection while preserving selections from OTHER pages
                        const newlySelected = Array.from(e.target.selectedOptions, o => o.value);
                        const currentVisibleIds = new Set(rightPageData.map(v => v.vendId));

                        setSelRightIds(prev => {
                            // keep IDs that are NOT on the current page
                            const otherPageSelections = prev.filter(id => !currentVisibleIds.has(id));
                            return [...otherPageSelections, ...newlySelected];
                        });
                        setActiveSide('right');
                      }}
                      onClick={() => setActiveSide('right')}
                      disabled={!isEditable || saving || removing}
                    >
                      {rightPageData.map(v => (
                        <option key={v.vendId} value={v.vendId} style={optionStyle}>
                          {v.vendId} — {v.vendName} {v.queueForMail ? ' (Q)' : ''}
                        </option>
                      ))}
                    </CFormSelect>

                    {/* Right Side Pagination Controls */}
                    <div style={{ display: 'flex', justifyContent: 'space-between', alignItems: 'center', marginTop: '4px' }}>
                        <Button
                            color="inherit"
                            variant="outlined"
                            size="small"
                            disabled={rightPage === 0}
                            onClick={() => setRightPage(p => Math.max(0, p - 1))}
                            sx={{ fontSize: '0.7rem', padding: '2px 6px', minWidth: 'auto', textTransform: 'none', color: '#666', borderColor: '#ccc' }}
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
                            disabled={rightPage >= rightPageCount - 1}
                            onClick={() => setRightPage(p => Math.min(rightPageCount - 1, p + 1))}
                            sx={{ fontSize: '0.7rem', padding: '2px 6px', minWidth: 'auto', textTransform: 'none', color: '#666', borderColor: '#ccc' }}
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

export default EditFileSentTo;
