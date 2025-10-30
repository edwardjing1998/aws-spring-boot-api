import React, { useState, useEffect, useMemo } from 'react';
import { Box, IconButton, Tabs, Tab, Button } from '@mui/material';
import CloseIcon from '@mui/icons-material/Close';
import { CRow, CCol } from '@coreui/react';

import EditSysPrinGeneral   from '../sys-prin-config/EditSysPrinGeneral';
import EditReMailOptions    from '../sys-prin-config/EditReMailOptions';
import EditStatusOptions    from '../sys-prin-config/EditStatusOptions';
import EditFileReceivedFrom from '../sys-prin-config/EditFileReceivedFrom';
import EditFileSentTo       from '../sys-prin-config/EditFileSentTo';
import EditSysPrinNotes     from '../sys-prin-config/EditSysPrinNotes';

const titleByMode = {
  new: 'New Sys/Prin',
  edit: 'Edit Sys/Prin',
  duplicate: 'Duplicate Sys/Prin',
  move: 'Move Sys/Prin',
  changeAll: 'Change Sys/Prin',
};

 const SysPrinInformationWindow = ({
   onClose,
   mode,
   selectedData,
   setSelectedData,
   selectedGroupRow,
   setSelectedGroupRow,
   // existing focused updaters
   onChangeVendorReceivedFrom,
   onChangeVendorSentTo,
   onPatchSysPrinsList, // NEW: bubble to the real owner of sysPrinsList
 }) => {
  const [tabIndex, setTabIndex] = useState(0);
  const [isEditable, setIsEditable] = useState(true);
  const [statusMap, setStatusMap] = useState({});

  const [saving, setSaving] = useState(false);

  useEffect(() => {
    setIsEditable(mode === 'edit' || mode === 'new');
  }, [mode]);

  const maxTabs = 6;

  const buildCreatePayload = useMemo(() => {
  const sd = selectedData ?? {};

  // If your create API truly needs a space for "none", you can map here:
  const normalizeDestroy = (v) => v == null ? ' ' : v; // or keep as-is if your API accepts '0'

  return {
    // identity
    client:   selectedGroupRow.client || '',
    sysPrin:  sd.sysPrin || '',

    // General tab
    custType:       sd.custType ?? '0',
    returnStatus:   sd.returnStatus ?? '',
    destroyStatus:  normalizeDestroy(sd.destroyStatus ?? '0'),
    special:        sd.special ?? '0',
    pinMailer:      sd.pinMailer ?? '0',
    active:         !!sd.active,                 // boolean
    rps:            String(sd.rps ?? '0'),       // '0' | '1'
    addrFlag:       String(sd.addrFlag ?? '0'),  // '0' | '1'
    astatRch:       String(sd.astatRch ?? '0'),  // '0' | '1'
    nm13:           String(sd.nm13 ?? '0'),      // '0' | '1'
    notes:          sd.notes ?? '',

    // ReMail options tab
    undeliverable:      sd.undeliverable ?? '0',
    poBox:              sd.poBox ?? '0',
    tempAway:           Number(sd.tempAway ?? 0) || 0,
    tempAwayAtts:       Number(sd.tempAwayAtts ?? 0) || 0,
    reportMethod:       Number(sd.reportMethod ?? 0) || 0,
    nonUS:              sd.nonUS ?? '0',
    holdDays:           Number(sd.holdDays ?? 0) || 0,
    forwardingAddress:  sd.forwardingAddress ?? '0',
    contact:            sd.contact ?? '',
    phone:              sd.phone ?? '',
    entityCode:         sd.entityCode ?? '0',
    session:            sd.session ?? '',
    badState:           sd.badState ?? '0',
    // (invalidDelivAreas is managed by its own APIs, so usually omitted here)

    // Status options tab
    statA: String(sd.statA ?? '0'),
    statB: String(sd.statB ?? '0'),
    statC: String(sd.statC ?? '0'),
    statD: String(sd.statD ?? '0'),
    statE: String(sd.statE ?? '0'),
    statF: String(sd.statF ?? '0'),
    statI: String(sd.statI ?? '0'),
    statL: String(sd.statL ?? '0'),
    statO: String(sd.statO ?? '0'),
    statU: String(sd.statU ?? '0'),
    statX: String(sd.statX ?? '0'),
    statZ: String(sd.statZ ?? '0'),
  };
}, [selectedData]);

// Save handler — POST /create/{client}/{sysPrin}
   // Save handler — POST /create/{client}/{sysPrin}
   const handleSaveCreate = async () => {
     const client = selectedGroupRow?.client?.toString().trim();
     const sysPrin = selectedData?.sysPrin?.toString().trim();

     if (!client || !sysPrin) {
       alert('Client and SysPrin are required to create a new record.');
       return;
     }

     const url = `http://localhost:8089/client-sysprin-writer/api/sysprins/create/${encodeURIComponent(client)}/${encodeURIComponent(sysPrin)}`;
    const payload = buildCreatePayload; // useMemo already returns the object

     setSaving(true);
     try {
       const res = await fetch(url, {
         method: 'POST',
         headers: { accept: '*/*', 'Content-Type': 'application/json' },
         body: JSON.stringify(payload),
       });

       if (!res.ok) {
         let msg = `Create failed (${res.status})`;
         try {
           const ct = res.headers.get('Content-Type') || '';
           if (ct.includes('application/json')) {
             const j = await res.json();
             msg = j?.message || JSON.stringify(j);
           } else {
             msg = await res.text();
           }
         } catch { /* ignore parse errors */ }
         throw new Error(msg);
       }

       // If API returns created entity, you can merge it into state:
       let created;
       try { created = await res.json(); } catch { /* some APIs return empty body */ }

      // --- Normalize the data we’ll propagate upward
      const canonical = created && typeof created === 'object' ? created : payload;
      // ensure identity fields are set
      canonical.client = client;
      canonical.sysPrin = sysPrin;
      // some lists use an id object
      const withId = {
        ...canonical,
        id: canonical.id ?? { client, sysPrin },
      };

      setSelectedData(prev => ({ ...(prev ?? {}), ...withId }));

      // keep grid/canonical list in parent synced (owner of sysPrinsList)
      if (typeof onPatchSysPrinsList === 'function') {
        onPatchSysPrinsList(sysPrin, withId, client);
      }

      // also update parent’s selectedGroupRow (if parent provided a setter)
      if (typeof setSelectedGroupRow === 'function') {
        setSelectedGroupRow(prev => {
          const prevId = prev?.id ?? {};
          return {
            ...(prev ?? {}),
            ...withId,
            id: { client, sysPrin, ...prevId },
          };
        });
      }

       alert('Sys/Prin created successfully.');
       // you could also close the modal here if desired:
       // onClose?.();
     } catch (e) {
       console.error(e);
       alert(e?.message || 'Failed to create Sys/Prin.');
     } finally {
       setSaving(false);
     }
   };

 // Focused updater for "general" fields (contact/phone/flags/dropdowns/etc.)
 const onChangeGeneral = (patchOrFn) => {
   setSelectedData((prev) => {
     const rawPatch = typeof patchOrFn === 'function' ? patchOrFn(prev) : (patchOrFn || {});
     const patch = Object.fromEntries(Object.entries(rawPatch).filter(([, v]) => v !== undefined));
     const next  = { ...(prev ?? {}), ...patch };

     const keySysPrin = patch.sysPrin ?? next.sysPrin ?? prev?.sysPrin ?? '';
     const keyClient  = patch.client  ?? next.client  ?? prev?.client  ?? undefined;

     // keep your local shadow list in selectedData (so the window reflects instantly)
     const listFromPrev = Array.isArray(prev?.sysPrins) ? prev.sysPrins : [];
     const listFromNext = Array.isArray(next?.sysPrins) ? next.sysPrins : [];
     const list = [...(listFromPrev.length ? listFromPrev : listFromNext)];
     const matchFn = (sp) => {
       const spCode   = sp?.id?.sysPrin ?? sp?.sysPrin;
       const spClient = sp?.id?.client  ?? sp?.client;
       if (!spCode) return false;
       return keyClient != null ? (spCode === keySysPrin && spClient === keyClient) : (spCode === keySysPrin);
     };
     const idx = list.findIndex(matchFn);
     if (idx >= 0) {
       list[idx] = { ...list[idx], ...patch };
       next.sysPrins = list;
     } else if (list.length) {
       next.sysPrins = list;
     }

     if (next.selectedGroupRow) {
       const sg = next.selectedGroupRow;
       const sgCode   = sg?.id?.sysPrin ?? sg?.sysPrin;
       const sgClient = sg?.id?.client  ?? sg?.client;
       if (sgCode === keySysPrin && (keyClient != null ? sgClient === keyClient : true)) {
         next.selectedGroupRow = { ...sg, ...patch };
       }
     }

    // 🔁 CRITICAL: forward the patch to the real owner of the grid list
    if (typeof onPatchSysPrinsList === 'function' && keySysPrin) {
      onPatchSysPrinsList(keySysPrin, patch, keyClient);
    }

     return next;
   });
 };

  const primaryLabel =
    mode === 'changeAll' ? 'Change All' :
    mode === 'duplicate' ? 'Duplicate' :
    mode === 'move' ? 'Move' :
    'Save';

    const handlePrimaryClick = async () => {
    if (mode === 'new') {
      await handleSaveCreate();
    } else {
      // You can implement update/other actions here later
      alert('Not in "new" mode. No create action executed.');
    }
  };

  return (
    <Box sx={{ p: 2, height: '100%' }}>
      {/* Header */}
      <Box
        sx={{
          display: 'flex', justifyContent: 'space-between', alignItems: 'center',
          bgcolor: '#1976d2', color: '#fff', px: 2, py: 1, mx: -4, mt: -4, borderRadius: 0,
        }}
      >
        <span style={{ fontSize: '0.9rem', fontWeight: 500 }}>
          {titleByMode[mode] ?? 'Sys/Prin'}
        </span>
        <IconButton onClick={onClose} size="small" sx={{ color: '#fff' }}>
          <CloseIcon fontSize="small" />
        </IconButton>
      </Box>

      <Box
        sx={{
          display: 'flex', justifyContent: 'space-between', alignItems: 'center',
          mt: '20px', backgroundColor: '#f3f6f8', height: '50px', width: '100%', px: 2, gap: 2,
        }}
      >
        <input
          type="text"
          placeholder="Enter Sys/Prin Name"
          value={selectedData?.sysPrin || ''}
          onChange={(e) => setSelectedData((prev) => ({ ...prev, sysPrin: e.target.value }))}
          disabled={!isEditable}
          style={{
            fontSize: '0.9rem', fontWeight: 400, width: '30vw', height: '30px',
            border: '1px solid #ccc', borderRadius: '4px', paddingLeft: '8px', backgroundColor: 'white',
          }}
        />
      </Box>

      <Tabs
        value={tabIndex}
        onChange={(_, v) => setTabIndex(v)}
        variant="scrollable"
        scrollButtons="auto"
        sx={{ mt: 1, mb: 2 }}
      >
        <Tab label="General"             sx={{ textTransform: 'none', minWidth: 120, fontSize: '0.78rem' }} />
        <Tab label="Remail Options"      sx={{ textTransform: 'none', minWidth: 160, fontSize: '0.78rem' }} />
        <Tab label="Status Options"      sx={{ textTransform: 'none', minWidth: 160, fontSize: '0.78rem' }} />
        <Tab label="File Received From"  sx={{ textTransform: 'none', minWidth: 160, fontSize: '0.78rem' }} />
        <Tab label="File Sent To"        sx={{ textTransform: 'none', minWidth: 160, fontSize: '0.78rem' }} />
        <Tab label="SysPrin Note"        sx={{ textTransform: 'none', minWidth: 160, fontSize: '0.78rem' }} />
      </Tabs>

      <Box sx={{ minHeight: '400px', mt: 2 }}>
        {tabIndex === 0 && (
          <EditSysPrinGeneral
            key={`general-${selectedData?.sysPrin ?? ''}`}
            selectedData={selectedData}
            setSelectedData={setSelectedData}
            isEditable={isEditable}
            onChangeGeneral={onChangeGeneral}
          />
        )}

         {tabIndex === 1 && (
            <EditReMailOptions
              key={`remail-${selectedData?.sysPrin ?? ''}`}
              selectedData={selectedData}
              setSelectedData={setSelectedData}
              isEditable={isEditable}
              onChangeGeneral={onChangeGeneral}   // 👈 forward the focused updater
            />
          )}

        {tabIndex === 2 && (
          <EditStatusOptions
            selectedData={selectedData}
            statusMap={statusMap}
            setStatusMap={setStatusMap}
            isEditable={isEditable}
            onChangeGeneral={onChangeGeneral}   // ⬅️ Pass this
          />
        )}

        {tabIndex === 3 && (
          <EditFileReceivedFrom
            key={`received-from-${selectedData?.sysPrin ?? ''}`}
            selectedData={selectedData}
            isEditable={isEditable}
            onChangeVendorReceivedFrom={onChangeVendorReceivedFrom}
            setSelectedData={setSelectedData}
          />
        )}

        {tabIndex === 4 && (
          <EditFileSentTo
            key={`sent-to-${selectedData?.sysPrin ?? ''}`}
            selectedData={selectedData}
            isEditable={isEditable}
            onChangeVendorSentTo={onChangeVendorSentTo}
            setSelectedData={setSelectedData}
          />
        )}

        {tabIndex === 5 && (
          <EditSysPrinNotes
            selectedData={selectedData}
            setSelectedData={setSelectedData}
            isEditable={isEditable}
          />
        )}
      </Box>

      <CRow className="mt-3">
        <CCol style={{ display: 'flex', justifyContent: 'flex-start' }}>
          <Button variant="outlined" size="small" onClick={() => setTabIndex(i => Math.max(i - 1, 0))}>
            Back
          </Button>
        </CCol>

        <CCol style={{ display: 'flex', justifyContent: 'flex-end', gap: '12px' }}>
          <Button variant="contained" size="small" onClick={handlePrimaryClick}>
            {mode === 'changeAll' ? 'Change All' : mode === 'duplicate' ? 'Duplicate' : mode === 'move' ? 'Move' : 'Create'}
          </Button>
          <Button variant="outlined" size="small" onClick={() => setTabIndex(i => Math.min(i + 1, 5))}>
            Next
          </Button>
        </CCol>
      </CRow>
    </Box>
  );
};

export default SysPrinInformationWindow;





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

  // ---- fetch initial clients (paged) ----
  useEffect(() => {
    fetchClientsPaging(currentPage, 25)
      .then((data) => setClientList(Array.isArray(data) ? data : []))
      .catch((error) => {
        console.error('Error fetching clients:', error);
        alert(`Error fetching client details: ${error.message}`);
      });
  }, [currentPage]);

  // ---- map: billingSp -> client record ----
  const clientMap = useMemo(() => {
    const map = new Map();
    clientList.forEach((client) => {
      map.set(client.billingSp, client);
    });
    return map;
  }, [clientList]);

  // ---- autocomplete callback replaces client list ----
  const handleClientsFetched = (fetchedClients) => {
    const list = Array.isArray(fetchedClients) ? fetchedClients : [];
    setCurrentPage(0);
    setClientList((prev) => {
      const prevIds = prev.map((c) => c.client).join(',');
      const newIds = list.map((c) => c.client).join(',');
      return prevIds === newIds ? prev : list;
    });
  };

  // ---- when user clicks rows in the nav grid ----
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
      selectedData,
      rowData,
      atmCashPrefixes,
      clientEmails,
      reportOptions,
      sysPrinsList
    );
    setSelectedData(mappedData);
  };

  // =========================
  // Helpers for syncing edits
  // =========================

  // Upsert a client into clientList by client id
  const upsertClient = useCallback((list, saved) => {
    if (!saved || !saved.client) return list;
    const idx = list.findIndex((c) => c.client === saved.client);
    if (idx >= 0) {
      const copy = [...list];
      copy[idx] = { ...copy[idx], ...saved };
      return copy;
    }
    // Insert new client at top; adjust as you wish
    return [saved, ...list];
  }, []);

  // Normalize a vendor record into the parent "canonical" shape
  const normalizeVendorSliceItem = useCallback(
    (v) => {
      const id = String(v?.vendorId ?? v?.vendId ?? v?.vendor?.vendId ?? v?.vendor?.id ?? '');
      const name =
        v?.vendName ??
        v?.vendorName ??
        v?.vendor?.vendNm ??
        v?.vendor?.name ??
        String(id);
      const q =
        typeof (v?.queueForMail ?? v?.queForMail ?? v?.queForMailCd) === 'string'
          ? ['1', 'Y', 'TRUE'].includes(String(v?.queueForMail ?? v?.queForMail ?? v?.queForMailCd).toUpperCase())
          : !!(v?.queueForMail ?? v?.queForMail);

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
    },
    [selectedData?.sysPrin]
  );

  // Patch the matching sysPrin object inside clientList (source-of-truth used by handleRowClick)
  const patchSysPrinSlice = useCallback(
    (sysPrin, sliceName, nextArrayRaw) => {
      if (!sysPrin) return;
      const nextArray = (Array.isArray(nextArrayRaw) ? nextArrayRaw : []).map(normalizeVendorSliceItem);

      setClientList((prev) =>
        prev.map((client) => {
          if (!Array.isArray(client?.sysPrins)) return client;
          const nextSysPrins = client.sysPrins.map((sp) => {
            const spName = sp?.sysPrin ?? sp?.id?.sysPrin;
            if (spName === sysPrin) {
              return { ...sp, [sliceName]: nextArray };
            }
            return sp;
          });
          return { ...client, sysPrins: nextSysPrins };
        })
      );
    },
    [normalizeVendorSliceItem]
  );

  // Focused updaters passed to the editor window/tabs
  const onChangeVendorReceivedFrom = useCallback(
    (nextList) => {
      setSelectedData((prev) => ({
        ...(prev ?? {}),
        vendorReceivedFrom: (Array.isArray(nextList) ? nextList : []).map(normalizeVendorSliceItem),
      }));
      const sp = String(selectedData?.sysPrin ?? '');
      patchSysPrinSlice(sp, 'vendorReceivedFrom', nextList);
    },
    [patchSysPrinSlice, selectedData?.sysPrin, normalizeVendorSliceItem]
  );

  const onChangeVendorSentTo = useCallback(
    (nextList) => {
      setSelectedData((prev) => ({
        ...(prev ?? {}),
        vendorSentTo: (Array.isArray(nextList) ? nextList : []).map(normalizeVendorSliceItem),
      }));
      const sp = String(selectedData?.sysPrin ?? '');
      patchSysPrinSlice(sp, 'vendorSentTo', nextList);
    },
    [patchSysPrinSlice, selectedData?.sysPrin, normalizeVendorSliceItem]
  );

    // NEW: apply email list changes from the modal to both clientList and selectedGroupRow
  const handleClientEmailsChanged = React.useCallback((clientId, nextEmailList) => {
    if (!clientId) return;

    setClientList((prev) =>
      prev.map((c) =>
        c.client === clientId ? { ...c, clientEmail: Array.isArray(nextEmailList) ? nextEmailList : [] } : c
      )
    );

    setSelectedGroupRow((prev) =>
      prev?.client === clientId ? { ...(prev ?? {}), clientEmail: Array.isArray(nextEmailList) ? nextEmailList : [] } : prev
    );
  }, []);

  // Force a clean remount of the SysPrin modal content when switching sysPrin
  const sysPrinKey = String(selectedData?.sysPrin ?? 'none');

  const [sysPrinsList, setSysPrinsList] = useState([]);

  const onPatchSysPrinsList = (sysPrin, patch, client) => {
  setSysPrinsList(prev => {
    const list = Array.isArray(prev) ? [...prev] : [];
    const idx = list.findIndex(sp => (sp?.id?.sysPrin ?? sp?.sysPrin) === sysPrin
                                   && (client ? (sp?.id?.client ?? sp?.client) === client : true));
    if (idx >= 0) {
      list[idx] = { ...list[idx], ...patch, id: { client, sysPrin, ...(list[idx]?.id ?? {}) } };
    } else {
      list.push({ ...patch, id: { client, sysPrin } });
    }
    return list;
  });
};


  // === NEW: handlers to receive created/updated client from the window ===
  const handleClientCreated = useCallback((saved) => {
    if (!saved) return;
    setClientList((prev) => upsertClient(prev, saved));
    // focus the newly created row in the right pane
    setSelectedGroupRow(saved);
    // optionally switch the window to edit mode after creation
    setClientInformationWindow({ open: true, mode: 'edit' });
  }, [upsertClient]);

  const handleClientUpdated = useCallback((saved) => {
    if (!saved) return;
    setClientList((prev) => upsertClient(prev, saved));
    setSelectedGroupRow((prev) => ({ ...(prev ?? {}), ...(saved ?? {}) }));
  }, [upsertClient]);

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
            // wire up (optional) existing create/update callbacks if you use them
            onClientCreated={(saved) => {
              if (!saved) return;
              setClientList((prev) => {
                const idx = prev.findIndex((c) => c.client === saved.client);
                if (idx >= 0) {
                  const copy = [...prev];
                  copy[idx] = { ...copy[idx], ...saved };
                  return copy;
                }
                return [saved, ...prev];
              });
              setSelectedGroupRow(saved);
              setClientInformationWindow({ open: true, mode: 'edit' });
            }}

            onClientUpdated={(saved) => {
              if (!saved) return;

              // If the API sometimes returns text/thin objects, you may want to normalize first:
              const obj = typeof saved === 'object' && saved ? saved : { client: selectedGroupRow?.client, ...saved };

              // 1) Upsert into the left list
              setClientList((prev) => {
                const idx = prev.findIndex(
                  (c) =>
                    String(c?.client ?? '') === String(obj?.client ?? '') ||
                    (obj?.billingSp && String(c?.billingSp ?? '') === String(obj?.billingSp ?? ''))
                );
                if (idx === -1) return prev;
                const next = [...prev];
                next[idx] = { ...next[idx], ...obj };
                return next;
              });

              // 2) Patch the preview card (preserve heavy slices if the update response is thin)
              setSelectedGroupRow((prev) => {
                if (!prev) return obj;
                return {
                  ...prev,
                  ...obj,
                  clientEmail:       obj.clientEmail       ?? prev.clientEmail,
                  sysPrins:          obj.sysPrins          ?? prev.sysPrins,
                  sysPrinsPrefixes:  obj.sysPrinsPrefixes  ?? prev.sysPrinsPrefixes,
                  reportOptions:     obj.reportOptions     ?? prev.reportOptions,
                };
              });
            }}



            // NEW: emails change bubbled up from child
            onClientEmailsChanged={handleClientEmailsChanged}
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
            key={sysPrinKey}
            onClose={() => setSysPrinInformationWindow({ open: false, mode: 'edit' })}
            mode={sysPrinInformationWindow.mode}
            selectedData={selectedData}
            setSelectedData={setSelectedData}
            selectedGroupRow={selectedGroupRow}
            setSelectedGroupRow={setSelectedGroupRow}
            onChangeVendorReceivedFrom={onChangeVendorReceivedFrom}
            onChangeVendorSentTo={onChangeVendorSentTo}
            onPatchSysPrinsList={onPatchSysPrinsList}
          />
        </Box>
      </Modal>
    </div>
  );
};

export default ClientInformationPage;
