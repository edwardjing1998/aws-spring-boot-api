/**
 * Maps the raw data from the grid row and related lists into the full selectedData object.
 * @param prev The previous selectedData state.
 * @param rowData The row data clicked in the grid.
 * @param atmCashPrefixes The sysPrinsPrefixes list.
 * @param clientEmails The clientEmail list.
 * @param reportOptions The reportOptions list.
 * @param sysPrinsList The sysPrins list.
 * @returns Updated selectedData object.
 */
export function mapRowDataToSelectedData(prev, rowData, atmCashPrefixes, clientEmails, reportOptions, sysPrinsList) {
    const matchedSysPrin = sysPrinsList.find(sp =>
        sp?.id?.sysPrin === rowData.sysPrin || sp?.sysPrin === rowData.sysPrin
    );

    return {
        ...prev,
        ...rowData,
        sysPrinsPrefixes: atmCashPrefixes,
        clientEmail: clientEmails,
        reportOptions: reportOptions,
        sysPrins: sysPrinsList,
        sysPrin: rowData.sysPrin || '',
        invalidDelivAreas: matchedSysPrin?.invalidDelivAreas || [],
        vendorReceivedFrom: matchedSysPrin?.vendorReceivedFrom || [],
        vendorSentTo: matchedSysPrin?.vendorSentTo || [],
        notes: rowData.notes || matchedSysPrin?.notes || '',
        statA: rowData.statA || matchedSysPrin?.statA || '',
        statB: rowData.statB || matchedSysPrin?.statB || '',
        statC: rowData.statC || matchedSysPrin?.statC || '',
        statE: rowData.statE || matchedSysPrin?.statE || '',
        statF: rowData.statF || matchedSysPrin?.statF || '',
        statI: rowData.statI || matchedSysPrin?.statI || '',
        statL: rowData.statL || matchedSysPrin?.statL || '',
        statU: rowData.statU || matchedSysPrin?.statU || '',
        statD: rowData.statD || matchedSysPrin?.statD || '',
        statO: rowData.statO || matchedSysPrin?.statO || '',
        statX: rowData.statX || matchedSysPrin?.statX || '',
        statZ: rowData.statZ || matchedSysPrin?.statZ || '',
        special: rowData.special || matchedSysPrin?.special || '',
        pinMailer: rowData.pinMailer || matchedSysPrin?.pinMailer || '',
        destroyStatus: rowData.destroyStatus || matchedSysPrin?.destroyStatus || '',
        custType: rowData.custType || matchedSysPrin?.custType || '',
        returnStatus: rowData.returnStatus || matchedSysPrin?.returnStatus || '',
        addrFlag: rowData.addrFlag || matchedSysPrin?.addrFlag || '',
        astatRch: rowData.astatRch || matchedSysPrin?.astatRch || '',
        active: rowData.active || matchedSysPrin?.active || '',
        nm13: rowData.nm13 || matchedSysPrin?.nm13 || '',
        tempAway: rowData.tempAway || matchedSysPrin?.tempAway || '',
        holdDays: rowData.holdDays || matchedSysPrin?.holdDays || '',
        tempAwayAtts: rowData.tempAwayAtts || matchedSysPrin?.tempAwayAtts || '',
        undeliverable: rowData.undeliverable || matchedSysPrin?.undeliverable || '',
        forwardingAddress: rowData.forwardingAddress || matchedSysPrin?.forwardingAddress || '',
        nonUS: rowData.nonUS || matchedSysPrin?.nonUS || '',
        poBox: rowData.poBox || matchedSysPrin?.poBox || '',
        badState: rowData.badState || matchedSysPrin?.badState || '',

    };
}

export const defaultSelectedData = {
  client: '',
  name: '',
  address: '',
  billingSp: '',
  atmCashRule: '',
  notes: '',
  special: '',
  pinMailer: '',
  destroyStatus: '',
  custType: '',
  returnStatus: '',
  addrFlag: '',
  astatRch: '',
  active: '',
  nm13: '',
  sysPrinsPrefixes: [],
  clientEmail: [],
  reportOptions: [],
  sysPrins: [],
  sysPrin: '',
  invalidDelivAreas: [],
  vendorReceivedFrom: [],
  vendorSentTo: [],
  statA: '',
  statB: '',
  statC: '',
  statE: '',
  statF: '',
  statI: '',
  statL: '',
  statU: '',
  statD: '',
  statO: '',
  statX: '',
  statZ: '',
};










import React, { useState, useEffect, useMemo } from 'react';
import {
  CRow,
  CCol,
  CCard,
  CCardBody
} from '@coreui/react';

import { Button } from '@mui/material';

import AutoCompleteInputBox from '../../../components/ClientAutoCompleteInputBox';
import PreviewSysPrinInformation from './sys-prin-config/PreviewSysPrinInformation';
import PreviewClientInformation from './PreviewClientInformation';
import { defaultSelectedData, mapRowDataToSelectedData } from './utils/SelectedData';
import NavigationPanel from './NavigationPanel';
import { fetchClientsPaging, fetchWildcardPage } from './utils/ClientIntegrationService';

import { Modal, Box } from '@mui/material';
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
      .then((data) => {
        setClientList(data);
      })
      .catch((error) => {
        console.error('Error fetching clients:', error);
        alert(`Error fetching client details: ${error.message}`);
      });
  }, [currentPage]);

  const clientMap = useMemo(() => {
    const map = new Map();
    clientList.forEach(client => {
      map.set(client.billingSp, client);
    });
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
            sx={{
                '& .MuiOutlinedInput-root': {
                  '& fieldset': {
                    border: 'none',
                  },
                },
              }}
          />
        </CCol>

        <CCol style={{ display: 'flex', alignItems: 'center', justifyContent: 'flex-start', border: 'none', maxWidth: '71%' }}>
          {/* Main Content
          <BusinessIcon style={{ marginLeft: '8px', fontSize: '18px' }} />
           */}
          <p style={{ margin: 0, marginLeft: '20px', fontWeight: '800' }}>Client Information</p>
          <Button
            variant="outlined"
            onClick={() => setClientInformationWindow({ open: true, mode: 'delete' })}
            size="small"
            sx={{ fontSize: '0.78rem', marginLeft: 'auto', marginRight: '6px', textTransform: 'none' }}
            disabled={clientEditActionsDisabled}
          >
            Delete Client
          </Button>
          <Button
            variant="outlined"
            onClick={() => setClientInformationWindow({ open: true, mode: 'edit' })}
            size="small"
            sx={{ fontSize: '0.78rem', marginRight: '6px', textTransform: 'none' }}
            disabled={clientEditActionsDisabled}
          >
            Edit Client
          </Button>
          <Button
            variant="outlined"
            onClick={() => setClientInformationWindow({ open: true, mode: 'new' })}
            size="small"
            sx={{ fontSize: '0.78rem', textTransform: 'none' }}
          >
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
                 {/* Main Content
                 <BusinessIcon style={{ marginLeft: '8px', fontSize: '18px' }} />
                 */}
                <p style={{ margin: 0, marginLeft: '20px', fontWeight: '800' }}>SysPrin Information</p>
                </CCol>
                <CCol style={{ display: 'flex', alignItems: 'center', justifyContent: 'flex-start', maxWidth: '60%' }}>
                <p style={{ margin: 0, marginLeft: '20px', fontWeight: '800' }}>
                    
                             <Button
                                variant="outlined"
                                onClick={() => setSysPrinInformationWindow({ open: true, mode: 'changeAll' })}
                                size="small"
                                sx={{ fontSize: '0.78rem', marginRight: '6px', textTransform: 'none' }}
                              >
                                Change All
                              </Button>
                              <Button
                                variant="outlined"
                                onClick={() => setSysPrinInformationWindow({ open: true, mode: 'edit' })}
                                size="small"
                                sx={{ fontSize: '0.78rem', textTransform: 'none', marginRight: '6px' }}
                              >
                                Edit SysPrin
                              </Button>
                              <Button
                                variant="outlined"
                                onClick={() => setSysPrinInformationWindow({ open: true, mode: 'new' })}
                                size="small"
                                sx={{ fontSize: '0.78rem', marginRight: '6px', textTransform: 'none' }}
                              >
                                New SysPrin
                              </Button>
                              <Button
                                variant="outlined"
                                onClick={() => setSysPrinInformationWindow({ open: true, mode: 'duplicate' })}
                                size="small"
                                sx={{ fontSize: '0.78rem', marginRight: '6px', textTransform: 'none' }}
                              >
                                Duplicate
                              </Button>
                              <Button
                                variant="outlined"
                                onClick={() => setSysPrinInformationWindow({ open: true, mode: 'move' })}
                                size="small"
                                sx={{ fontSize: '0.78rem', marginRight: '6px', textTransform: 'none' }}
                              >
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
              <Box
                sx={{
                  position: 'absolute',
                  top: '50%',
                  left: '50%',
                  transform: 'translate(-50%, -50%)',
                  width: '860px',
                  height: '680px',
                  bgcolor: 'background.paper',
                  boxShadow: 24,
                  borderRadius: 2,
                  p: 2,
                  overflow: 'visible',
                  maxHeight: 'none',
                }}
              >
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
                <Box
                    sx={{
                    position: 'absolute',
                    top: '50%',
                    left: '50%',
                    transform: 'translate(-50%, -50%)',
                    width: '960px',
                    maxHeight: '90vh',
                    bgcolor: 'background.paper',
                    boxShadow: 24,
                    borderRadius: 2,
                    p: 2,
                    overflowY: 'auto',
                    }}
                >
                    <SysPrinInformationWindow
                      onClose={() => setSysPrinInformationWindow({ open: false, mode: 'edit' })}
                      mode={sysPrinInformationWindow.mode}
                      selectedData={selectedData}
                      setSelectedData={setSelectedData}
                      selectedGroupRow={selectedGroupRow}
                    />
                </Box>
            </Modal>
    </div>
  );
};

export default ClientInformationPage;





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





