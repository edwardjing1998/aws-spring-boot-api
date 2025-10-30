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
  const handleSaveCreate = async () => {
    const client = selectedGroupRow?.client?.toString().trim();
    const sysPrin = selectedData?.sysPrin?.toString().trim();

    if (!client || !sysPrin) {
      alert('Client and SysPrin are required to create a new record.');
      return;
    }

    const url = `http://localhost:8089/client-sysprin-writer/api/sysprins/create/${encodeURIComponent(client)}/${encodeURIComponent(sysPrin)}`;
    const payload = buildCreatePayload;

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

      // reflect locally so the UI shows the new values immediately
      if (created && typeof created === 'object') {
        setSelectedData(prev => ({ ...(prev ?? {}), ...created }));
        // If parent keeps a canonical list and you want to inject/patch it:
        if (typeof onPatchSysPrinsList === 'function') {
          onPatchSysPrinsList(sysPrin, created, client);
        }
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
