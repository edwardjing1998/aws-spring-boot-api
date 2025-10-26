import React, { useState, useEffect, useMemo, useCallback } from 'react';
import { CRow, CCol, CCard, CCardBody } from '@coreui/react';
import { Button, Modal, Box } from '@mui/material';

import AutoCompleteInputBox from '../../../components/ClientAutoCompleteInputBox';
import PreviewSysPrinInformation from './sys-prin-config/PreviewSysPrinInformation';
import PreviewClientInformation from './PreviewClientInformation';
import { defaultSelectedData, mapRowDataToSelectedData } from './utils/SelectedData';
import NavigationPanel from './NavigationPanel';
import { fetchClientsPaging, fetchWildcardPage } from './utils/ClientIntegrationService';

import ClientInformationWindow from './utils/ClientInformationWindow';
import SysPrinInformationWindow from './utils/SysPrinInformationWindow';

const ClientInformationPage = () => {
  const [clientList, setClientList] = useState([]);
  const [currentPage, setCurrentPage] = useState(0);
  const [inputValue, setInputValue] = useState('');
  const [isWildcardMode, setIsWildcardMode] = useState(false);
  const [selectedGroupRow, setSelectedGroupRow] = useState(null);
  const [selectedData, setSelectedData] = useState(defaultSelectedData);

  const [clientInformationWindow, setClientInformationWindow] = useState({ open: false, mode: 'edit' });
  const [sysPrinInformationWindow, setSysPrinInformationWindow] = useState({ open: false, mode: 'edit' });
  const [clientEditActionsDisabled, setClientEditActionsDisabled] = useState(true);

  useEffect(() => {
    fetchClientsPaging(currentPage, 25)
      .then((data) => setClientList(data))
      .catch((error) => {
        console.error('Error fetching clients:', error);
        alert(`Error fetching client details: ${error.message}`);
      });
  }, [currentPage]);

  const clientMap = useMemo(() => {
    const map = new Map();
    clientList.forEach(client => map.set(client.billingSp, client));
    return map;
  }, [clientList]);

  const handleClientsFetched = (fetchedClients) => {
    setCurrentPage(0);
    setClientList(prev => {
      const prevIds = prev.map(c => c.client).join(',');
      const newIds = fetchedClients.map(c => c.client).join(',');
      return prevIds === newIds ? prev : fetchedClients;
    });
  };

  const handleRowClick = (rowData) => {
    if (rowData.isGroup) {
      setClientEditActionsDisabled(false);
      setSelectedGroupRow(rowData);
      return;
    }
    const billingSp = rowData.billingSp || '';
    const matchedClient = clientMap.get(billingSp);
    const atmCashPrefixes = matchedClient?.sysPrinsPrefixes || [];
    const clientEmails = matchedClient?.clientEmail || [];
    const reportOptions = matchedClient?.reportOptions || [];
    const sysPrinsList = matchedClient?.sysPrins || [];

    const mappedData = mapRowDataToSelectedData(
      selectedData, rowData, atmCashPrefixes, clientEmails, reportOptions, sysPrinsList
    );
    setSelectedData(mappedData);
  };

  // -------- focused updaters for vendorReceivedFrom / vendorSentTo --------
  // keep parent as single source of truth; children call these
  const normalizeVRF = useCallback((v) => {
    const id = String(v.vendorId ?? v.vendId ?? '');
    const name = v.vendName ?? v.vendorName ?? '';
    const q = !!(v.queueForMail ?? v.queForMail);
    const sysPrin = String(selectedData?.sysPrin ?? '');
    return {
      vendorId: id,
      vendId: id,
      vendName: name,
      queueForMail: q,
      queForMail: q,
      queForMailCd: q ? 'Y' : 'N',
      vendor: { vendId: id, vendNm: name },
      ...(sysPrin ? { id: { sysPrin, vendorId: id } } : {}),
    };
  }, [selectedData?.sysPrin]);

  const updateVendorReceivedFrom = useCallback((updater) => {
    setSelectedData(prev => {
      const prevList = prev?.vendorReceivedFrom ?? [];
      const rawNext = typeof updater === 'function' ? updater(prevList) : updater;
      const nextList = (Array.isArray(rawNext) ? rawNext : []).map(normalizeVRF);
      return { ...(prev ?? {}), vendorReceivedFrom: nextList };
    });
  }, [normalizeVRF]);

  // (Optional if you’ll also wire SentTo the same way)
  const normalizeVST = useCallback((v) => {
    const id = String(v.vendorId ?? v.vendId ?? '');
    const name = v.vendName ?? v.vendorName ?? '';
    const q = !!(v.queueForMail ?? v.queForMail);
    const sysPrin = String(selectedData?.sysPrin ?? '');
    return {
      vendorId: id,
      vendId: id,
      vendName: name,
      queueForMail: q,
      queForMail: q,
      queForMailCd: q ? 'Y' : 'N',
      vendor: { vendId: id, vendNm: name },
      ...(sysPrin ? { id: { sysPrin, vendorId: id } } : {}),
    };
  }, [selectedData?.sysPrin]);

  const updateVendorSentTo = useCallback((updater) => {
    setSelectedData(prev => {
      const prevList = prev?.vendorSentTo ?? [];
      const rawNext = typeof updater === 'function' ? updater(prevList) : updater;
      const nextList = (Array.isArray(rawNext) ? rawNext : []).map(normalizeVST);
      return { ...(prev ?? {}), vendorSentTo: nextList };
    });
  }, [normalizeVST]);

  // force remount of sysprin window when switching records
  const sysPrinKey = String(selectedData?.sysPrin ?? 'none');

  return (
    <div className="d-flex flex-column" style={{ minHeight: '100vh', width: '80vw', overflow: 'visible' }}>
      {/* Input + Buttons */}
      <CRow className="px-3" style={{ marginBottom: '10px' }}>
        <CCol style={{ flex: '0 0 29%', maxWidth: '29%', paddingLeft: '0px', border: 'none' }}>
          <AutoCompleteInputBox
            inputValue={inputValue}
            setInputValue={setInputValue}
            onClientsFetched={handleClientsFetched}
            isWildcardMode={isWildcardMode}
            setIsWildcardMode={setIsWildcardMode}
            sx={{ '& .MuiOutlinedInput-root': { '& fieldset': { border: 'none' } } }}
          />
        </CCol>
        <CCol style={{ display: 'flex', alignItems: 'center', justifyContent: 'flex-start', border: 'none', maxWidth: '71%' }}>
          <p style={{ margin: 0, marginLeft: '20px', fontWeight: '800' }}>Client Information</p>
          <Button variant="outlined" onClick={() => setClientInformationWindow({ open: true, mode: 'delete' })} size="small"
                  sx={{ fontSize: '0.78rem', marginLeft: 'auto', marginRight: '6px', textTransform: 'none' }}
                  disabled={clientEditActionsDisabled}>
            Delete Client
          </Button>
          <Button variant="outlined" onClick={() => setClientInformationWindow({ open: true, mode: 'edit' })} size="small"
                  sx={{ fontSize: '0.78rem', marginRight: '6px', textTransform: 'none' }}
                  disabled={clientEditActionsDisabled}>
            Edit Client
          </Button>
          <Button variant="outlined" onClick={() => setClientInformationWindow({ open: true, mode: 'new' })} size="small"
                  sx={{ fontSize: '0.78rem', textTransform: 'none' }}>
            New Client
          </Button>
        </CCol>
      </CRow>

      {/* Main Content */}
      <CRow style={{ flexGrow: 1, paddingLeft: '0px', paddingRight: '12px' }}>
        {/* Navigation Panel */}
        <CCol style={{ flex: '0 0 30%', maxWidth: '30%' }}>
          <CCard style={{ height: '100%' }}>
            <CCardBody style={{ height: '100%', padding: 0 }}>
              <div style={{ height: '1200px', overflow: 'hidden' }}>
                <NavigationPanel
                  onRowClick={handleRowClick}
                  clientList={clientList}
                  setClientList={setClientList}
                  currentPage={currentPage}
                  setCurrentPage={setCurrentPage}
                  isWildcardMode={isWildcardMode}
                  setIsWildcardMode={setIsWildcardMode}
                  onFetchWildcardPage={fetchWildcardPage}
                />
              </div>
            </CCardBody>
          </CCard>
        </CCol>

        {/* Client + SysPrin Info */}
        <CCol style={{ flex: '0 0 70%', maxWidth: '70%' }}>
          <CCard style={{ height: '100%' }}>
            <CCardBody style={{ height: '100%', padding: 0 }}>
              <div style={{ height: '1000px', overflow: 'hidden' }}>
                <CRow className="p-3" style={{ height: '400px' }}>
                  <CCol xs={12} style={{ height: '100%' }}>
                    <PreviewClientInformation
                      setClientInformationWindow={setClientInformationWindow}
                      selectedGroupRow={selectedGroupRow}
                    />
                  </CCol>
                </CRow>

                <CRow style={{ height: '30px', marginBottom: '10px' }}>
                  <CCol style={{ display: 'flex', alignItems: 'center', justifyContent: 'flex-start', maxWidth: '40%' }}>
                    <p style={{ margin: 0, marginLeft: '20px', fontWeight: '800' }}>SysPrin Information</p>
                  </CCol>
                  <CCol style={{ display: 'flex', alignItems: 'center', justifyContent: 'flex-start', maxWidth: '60%' }}>
                    <p style={{ margin: 0, marginLeft: '20px', fontWeight: '800' }}>
                      <Button variant="outlined" onClick={() => setSysPrinInformationWindow({ open: true, mode: 'changeAll' })}
                              size="small" sx={{ fontSize: '0.78rem', marginRight: '6px', textTransform: 'none' }}>
                        Change All
                      </Button>
                      <Button variant="outlined" onClick={() => setSysPrinInformationWindow({ open: true, mode: 'edit' })}
                              size="small" sx={{ fontSize: '0.78rem', textTransform: 'none', marginRight: '6px' }}>
                        Edit SysPrin
                      </Button>
                      <Button variant="outlined" onClick={() => setSysPrinInformationWindow({ open: true, mode: 'new' })}
                              size="small" sx={{ fontSize: '0.78rem', marginRight: '6px', textTransform: 'none' }}>
                        New SysPrin
                      </Button>
                      <Button variant="outlined" onClick={() => setSysPrinInformationWindow({ open: true, mode: 'duplicate' })}
                              size="small" sx={{ fontSize: '0.78rem', marginRight: '6px', textTransform: 'none' }}>
                        Duplicate
                      </Button>
                      <Button variant="outlined" onClick={() => setSysPrinInformationWindow({ open: true, mode: 'move' })}
                              size="small" sx={{ fontSize: '0.78rem', marginRight: '6px', textTransform: 'none' }}>
                        Move
                      </Button>
                    </p>
                  </CCol>
                </CRow>

                <CRow className="px-3" style={{ marginBottom: '20px', height: '500px' }}>
                  <CCol xs={12} style={{ height: '100%' }}>
                    <CCard style={{ height: '100%' }}>
                      <CCardBody style={{ padding: '10px', height: '100%', overflowY: 'auto' }}>
                        <PreviewSysPrinInformation
                          setSysPrinInformationWindow={setSysPrinInformationWindow}
                          selectedData={selectedData}
                          selectedGroupRow={selectedGroupRow}
                        />
                      </CCardBody>
                    </CCard>
                  </CCol>
                </CRow>
              </div>
            </CCardBody>
          </CCard>
        </CCol>
      </CRow>

      {/* Drawers */}
      <Modal
        open={clientInformationWindow.open}
        onClose={() => setClientInformationWindow({ open: false, mode: 'edit' })}
        aria-labelledby="client-info-modal"
        aria-describedby="client-info-modal-description"
      >
        <Box sx={{
          position: 'absolute', top: '50%', left: '50%', transform: 'translate(-50%, -50%)',
          width: '860px', height: '680px', bgcolor: 'background.paper', boxShadow: 24, borderRadius: 2, p: 2,
          overflow: 'visible', maxHeight: 'none',
        }}>
          <ClientInformationWindow
            onClose={() => setClientInformationWindow({ open: false, mode: 'edit' })}
            selectedGroupRow={selectedGroupRow}
            setSelectedGroupRow={setSelectedGroupRow}
            mode={clientInformationWindow.mode}
          />
        </Box>
      </Modal>

      <Modal
        open={sysPrinInformationWindow.open}
        onClose={() => setSysPrinInformationWindow({ open: false, mode: 'edit' })}
        aria-labelledby="sysprin-info-modal"
        aria-describedby="sysprin-info-modal-description"
      >
        <Box sx={{
          position: 'absolute', top: '50%', left: '50%', transform: 'translate(-50%, -50%)',
          width: '960px', maxHeight: '90vh', bgcolor: 'background.paper', boxShadow: 24, borderRadius: 2, p: 2, overflowY: 'auto',
        }}>
          <SysPrinInformationWindow
            key={sysPrinKey}                       {/* force remount per record */}
            onClose={() => setSysPrinInformationWindow({ open: false, mode: 'edit' })}
            mode={sysPrinInformationWindow.mode}
            selectedData={selectedData}
            // keep setSelectedData for other fields if needed elsewhere,
            // but children should prefer the focused updaters:
            setSelectedData={setSelectedData}
            selectedGroupRow={selectedGroupRow}

            // >>> NEW: pass focused updaters <<<
            onChangeVendorReceivedFrom={updateVendorReceivedFrom}
            onChangeVendorSentTo={updateVendorSentTo}
          />
        </Box>
      </Modal>
    </div>
  );
};

export default ClientInformationPage;






import React from 'react';
import EditFileReceivedFrom from '../sys-prin-config/EditFileReceivedFrom';
// If you also have EditFileSentTo, import it similarly.

const SysPrinInformationWindow = ({
  onClose,
  mode,
  selectedData,
  setSelectedData,           // still available for other panes/fields
  selectedGroupRow,

  // >>> NEW focused updaters from parent <<<
  onChangeVendorReceivedFrom,
  onChangeVendorSentTo,
}) => {
  const isEditable = mode !== 'view';

  return (
    <div>
      {/* other sections… */}

      <EditFileReceivedFrom
        selectedData={selectedData}
        isEditable={isEditable}
        // >>> pass focused updater down <<<
        onChangeVendorReceivedFrom={onChangeVendorReceivedFrom}
      />

      {/* If you also render sent-to: */}
      {/* <EditFileSentTo
           selectedData={selectedData}
           isEditable={isEditable}
           onChangeVendorSentTo={onChangeVendorSentTo}
         /> */}
    </div>
  );
};

export default SysPrinInformationWindow;






import React, { useEffect, useMemo, useRef, useState } from 'react';
import { CCard, CCardBody, CCol, CRow, CButton, CFormSelect, CFormCheck } from '@coreui/react';

const EditFileReceivedFrom = ({ selectedData, onChangeVendorReceivedFrom, isEditable }) => {
  // --- helpers
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

  // --- state
  const sysPrin = String(selectedData?.sysPrin ?? '');
  const [allAvailable, setAllAvailable] = useState([]); // master pool (fileIo=O)
  const [right, setRight] = useState([]);               // assigned list
  const [selLeftIds, setSelLeftIds] = useState([]);
  const [selRightIds, setSelRightIds] = useState([]);
  const [activeSide, setActiveSide] = useState('left');
  const [adding, setAdding] = useState(false);
  const [removing, setRemoving] = useState(false);

  // seed RIGHT when parent slice changes
  useEffect(() => {
    const seeded = (selectedData?.vendorReceivedFrom ?? []).map(normalize).filter(v => v.vendId);
    setRight(seeded);
    setSelRightIds([]);
  }, [selectedData?.vendorReceivedFrom]);

  // load available vendors (fileIo=O)
  useEffect(() => {
    fetch('http://localhost:8089/client-sysprin-reader/api/vendor?fileIo=O')
      .then(r => r.json())
      .then(data => setAllAvailable((Array.isArray(data) ? data : []).map(normalize)))
      .catch(err => console.error('Failed to load vendors (O):', err));
  }, [sysPrin]);

  // derive left by subtracting right
  const left = useMemo(() => {
    const rightIds = new Set(right.map(v => v.vendId));
    return allAvailable.filter(v => !rightIds.has(v.vendId));
  }, [allAvailable, right]);

  // selections and tri-state
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
    activeSide === 'left' ? (selectedLeft.length === 0 ? false : leftAllTrue)
                          : (selectedRight.length === 0 ? false : rightAllTrue);

  const rightEnableCondition = selectedRight.length > 0 && rightAllTrue;
  const checkboxDisabled =
    !isEditable || adding || removing ||
    (activeSide === 'left' ? selectedLeft.length === 0 : !rightEnableCondition);

  const checkboxRef = useRef(null);
  useEffect(() => {
    if (checkboxRef.current) checkboxRef.current.indeterminate = isIndeterminate;
  }, [isIndeterminate]);

  const onToggleQueue = (e) => {
    if (activeSide === 'right') return; // read-only on right
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

  // ADD
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

          // sync parent (focused updater)
          onChangeVendorReceivedFrom?.(toParentShape(merged));
          return merged;
        });
      }

      setSelLeftIds([]);
      setActiveSide('left');
    } finally {
      setAdding(false);
    }
  };

  // REMOVE
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

        // sync parent (focused updater)
        onChangeVendorReceivedFrom?.(toParentShape(remainingRight));
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





