import React, { useState, useEffect, useMemo } from 'react';
import { Box, IconButton, Tabs, Tab, Button } from '@mui/material';
import CloseIcon from '@mui/icons-material/Close';
import { CRow, CCol } from '@coreui/react';
import TextField from '@mui/material/TextField';

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

// --- helpers to normalize flag types consistently everywhere ---
const as01 = (v) =>
  v === true || v === 1 || v === '1' || v === 'Y' ? '1' : '0';
const asYN = (v) =>
  v === true || v === 'Y' || v === '1' ? 'Y' : 'N';

const SysPrinInformationWindow = ({
  onClose,
  mode,
  selectedData,
  setSelectedData,
  selectedGroupRow,
  setSelectedGroupRow,
  // vendors & list patchers
  onChangeVendorReceivedFrom,
  onChangeVendorSentTo,
  onPatchSysPrinsList, // bubble to the real owner of sysPrinsList (grid)
}) => {
  const [tabIndex, setTabIndex] = useState(0);
  const [isEditable, setIsEditable] = useState(true);
  const [statusMap, setStatusMap] = useState({});
  const [saving, setSaving] = useState(false);

  // Move-mode inputs
  const [oldClientId, setOldClientId] = useState(() => selectedGroupRow?.client ?? selectedData?.client ?? '');
  const [newClientId, setNewClientId] = useState('');

  // Duplicate-mode input
  const [targetSysPrin, setTargetSysPrin] = useState('');

  useEffect(() => {
    setIsEditable(mode === 'edit' || mode === 'new');
  }, [mode]);

  // -----------------------------
  // Build payload from editor data
  // -----------------------------
  const buildCreatePayload = useMemo(() => {
    const sd = selectedData ?? {};
    const normalizeDestroy = (v) => (v == null ? '0' : v); // keep '0' default

    return {
      // identity
      client:  selectedGroupRow?.client || '',
      sysPrin: sd.sysPrin || '',

      // General tab
      custType:      sd.custType ?? '0',
      returnStatus:  sd.returnStatus ?? '',
      destroyStatus: normalizeDestroy(sd.destroyStatus ?? '0'),
      special:       sd.special ?? '0',
      pinMailer:     sd.pinMailer ?? '0',

      // flags normalized
      active:   as01(sd.active),     // '1' | '0'  ⬅️ FIXED (no more !!)
      rps:      asYN(sd.rps),        // 'Y' | 'N'
      addrFlag: asYN(sd.addrFlag),   // 'Y' | 'N'
      astatRch: as01(sd.astatRch),   // '1' | '0'
      nm13:     as01(sd.nm13),       // '1' | '0'

      notes:             sd.notes ?? '',
      undeliverable:     sd.undeliverable ?? '0',
      poBox:             sd.poBox ?? '0',
      tempAway:          Number(sd.tempAway ?? 0) || 0,
      tempAwayAtts:      Number(sd.tempAwayAtts ?? 0) || 0,
      reportMethod:      Number(sd.reportMethod ?? 0) || 0,
      nonUS:             sd.nonUS ?? '0',
      holdDays:          Number(sd.holdDays ?? 0) || 0,
      forwardingAddress: sd.forwardingAddress ?? '0',
      contact:           sd.sysPrinContact ?? '',
      phone:             sd.sysPrinPhone ?? '',
      entityCode:        sd.entityCode ?? '0',
      session:           sd.session ?? '',
      badState:          sd.badState ?? '0',

      // Status options tab (strings '1'/'0'—keep consistent for backend)
      statA: as01(sd.statA),
      statB: as01(sd.statB),
      statC: as01(sd.statC),
      statD: as01(sd.statD),
      statE: as01(sd.statE),
      statF: as01(sd.statF),
      statI: as01(sd.statI),
      statL: as01(sd.statL),
      statO: as01(sd.statO),
      statU: as01(sd.statU),
      statX: as01(sd.statX),
      statZ: as01(sd.statZ),
    };
  }, [selectedData, selectedGroupRow?.client]);

  // -----------------------------------------
  // Build the "Change All" patch from the form
  // -----------------------------------------
  const getCopyPatchFromSelectedData = () => {
    const base = buildCreatePayload || {};

    // Omit identity fields
    const OMIT = new Set([
      'client',
      'sysPrin',
      'id',
      // keep vendors/areas omitted unless you intentionally want to copy them too
      // 'vendorReceivedFrom',
      // 'vendorSentTo',
      // 'invalidDelivAreas',
    ]);

    const patch = Object.fromEntries(
      Object.entries(base).filter(([k]) => !OMIT.has(k))
    );

    // Normalize numeric-like values (safety no-ops if already normalized)
    if (patch.tempAway != null)       patch.tempAway       = Number(patch.tempAway) || 0;
    if (patch.tempAwayAtts != null)   patch.tempAwayAtts   = Number(patch.tempAwayAtts) || 0;
    if (patch.reportMethod != null)   patch.reportMethod   = Number(patch.reportMethod) || 0;
    if (patch.holdDays != null)       patch.holdDays       = Number(patch.holdDays) || 0;

    // Ensure 0/1 flags (including active!)
    [
      'active',
      'astatRch', 'nm13',
      'statA','statB','statC','statD','statE','statF','statI','statL','statO','statU','statX','statZ',
    ].forEach((k) => {
      if (k in patch) patch[k] = as01(patch[k]);
    });

    // Ensure Y/N fields
    ['rps', 'addrFlag', 'nonUS', 'forwardingAddress'].forEach((k) => {
      if (k in patch) patch[k] = asYN(patch[k]);
    });

    // Keep destroyStatus a single char
    if ('destroyStatus' in patch && (patch.destroyStatus == null || patch.destroyStatus === '')) {
      patch.destroyStatus = '0';
    }

    return patch;
  };

  // -------------------------
  // Create   — POST /create/…
  // -------------------------
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
          msg = ct.includes('application/json') ? (await res.json())?.message ?? msg : await res.text();
        } catch {}
        throw new Error(msg);
      }

      let created;
      try { created = await res.json(); } catch {}
      const canonical = (created && typeof created === 'object') ? created : payload;

      // ensure identity fields are set & include id
      const withId = {
        ...canonical,
        client,
        sysPrin,
        id: canonical.id ?? { client, sysPrin },
      };

      // Update local detail
      setSelectedData(prev => ({ ...(prev ?? {}), ...withId }));

      // Update grid/canonical list
      if (typeof onPatchSysPrinsList === 'function') {
        onPatchSysPrinsList(sysPrin, withId, client);
      }

      // Update selectedGroupRow if parent provided setter
      if (typeof setSelectedGroupRow === 'function') {
        setSelectedGroupRow(prev => {
          const prevId = prev?.id ?? {};
          return { ...(prev ?? {}), ...withId, id: { client, sysPrin, ...prevId } };
        });
      }

      alert('Sys/Prin created successfully.');
    } catch (e) {
      console.error(e);
      alert(e?.message || 'Failed to create Sys/Prin.');
    } finally {
      setSaving(false);
    }
  };

  // -------------------------
  // Move     — PUT /move-…
  // -------------------------
  const handleMove = async () => {
    const sysPrin = selectedData?.sysPrin?.toString().trim();
    const oldId = oldClientId?.toString().trim();
    const newId = newClientId?.toString().trim();

    if (!sysPrin || !oldId || !newId) {
      alert('Old Client ID, New Client ID, and SysPrin are required.');
      return;
    }

    const url =
      `http://localhost:8089/client-sysprin-writer/api/sysprins/move-sysprin` +
      `?oldClientId=${encodeURIComponent(oldId)}` +
      `&sysPrin=${encodeURIComponent(sysPrin)}` +
      `&newClientId=${encodeURIComponent(newId)}`;

    setSaving(true);
    try {
      const res = await fetch(url, { method: 'PUT', headers: { accept: '*/*' } });
      if (!res.ok) {
        let msg = `Move failed (${res.status})`;
        try {
          const ct = res.headers.get('Content-Type') || '';
          msg = ct.includes('application/json') ? (await res.json())?.message ?? msg : await res.text();
        } catch {}
        throw new Error(msg);
      }

      const moved = {
        ...(selectedData ?? {}),
        client: newId,
        sysPrin,
        id: { client: newId, sysPrin },
      };

      setSelectedData((prev) => ({ ...(prev ?? {}), ...moved }));

      if (typeof setSelectedGroupRow === 'function') {
        setSelectedGroupRow((prev) => ({ ...(prev ?? {}) }));
      }

      if (typeof onPatchSysPrinsList === 'function') {
        onPatchSysPrinsList(sysPrin, { __REMOVE__: true }, oldId);
        onPatchSysPrinsList(sysPrin, moved, newId);
      }

      alert('Sys/Prin moved successfully.');
    } catch (e) {
      console.error(e);
      alert(e?.message || 'Failed to move Sys/Prin.');
    } finally {
      setSaving(false);
    }
  };

  // -------------------------------------------
  // Duplicate — POST /duplicate-to/{target}…
  // -------------------------------------------
  const handleDuplicate = async () => {
    const clientId = (selectedGroupRow?.client ?? selectedData?.client ?? '').toString().trim();
    const source = (selectedData?.sysPrin ?? '').toString().trim();
    const target = (targetSysPrin ?? '').toString().trim();

    if (!clientId || !source || !target) {
      alert('Client, Source SysPrin, and Target SysPrin are required.');
      return;
    }
    if (source === target) {
      alert('Target SysPrin must be different from Source SysPrin.');
      return;
    }

    const url =
      `http://localhost:8089/client-sysprin-writer/api/clients/${encodeURIComponent(clientId)}` +
      `/sysprins/${encodeURIComponent(source)}` +
      `/duplicate-to/${encodeURIComponent(target)}` +
      `?overwrite=false&copyAreas=true`;

    setSaving(true);
    try {
      const res = await fetch(url, { method: 'POST', headers: { accept: '*/*' }, body: '' });
      if (!res.ok) {
        let msg = `Duplicate failed (${res.status})`;
        try {
          const ct = res.headers.get('Content-Type') || '';
          msg = ct.includes('application/json') ? (await res.json())?.message ?? msg : await res.text();
        } catch {}
        throw new Error(msg);
      }

      let dup;
      try { dup = await res.json(); } catch { dup = null; }
      const canonical = (dup && typeof dup === 'object') ? dup : { ...(selectedData ?? {}) };
      canonical.client = clientId;
      canonical.sysPrin = target;
      const withId = { ...canonical, id: canonical.id ?? { client: clientId, sysPrin: target } };

      setSelectedData(prev => ({ ...(prev ?? {}), ...withId }));

      if (typeof onPatchSysPrinsList === 'function') {
        onPatchSysPrinsList(target, withId, clientId);
      }

      if (typeof setSelectedGroupRow === 'function') {
        setSelectedGroupRow(prev => {
          const prevId = prev?.id ?? {};
          return { ...(prev ?? {}), ...withId, id: { client: clientId, sysPrin: target, ...prevId } };
        });
      }

      alert('Sys/Prin duplicated successfully.');
    } catch (e) {
      console.error(e);
      alert(e?.message || 'Failed to duplicate Sys/Prin.');
    } finally {
      setSaving(false);
    }
  };

  // -------------------------------------------
  // Change All — POST /clients/{client}/sysprins/copy-from/{source}
  // -------------------------------------------
  const handleChangeAll = async () => {
    const clientId = (selectedGroupRow?.client ?? selectedData?.client ?? '').toString().trim();
    const source = (selectedData?.sysPrin ?? '').toString().trim();

    if (!clientId || !source) {
      alert('Client and Source SysPrin are required.');
      return;
    }

    const url =
      `http://localhost:8089/client-sysprin-writer/api/clients/${encodeURIComponent(clientId)}` +
      `/sysprins/copy-from/${encodeURIComponent(source)}`;

    setSaving(true);
    try {
      const res = await fetch(url, {
        method: 'POST',
        headers: { accept: '*/*' },
        body: '', // endpoint per your curl
      });
      if (!res.ok) {
        let msg = `Change All failed (${res.status})`;
        try {
          const ct = res.headers.get('Content-Type') || '';
          msg = ct.includes('application/json') ? (await res.json())?.message ?? msg : await res.text();
        } catch {}
        throw new Error(msg);
      }

      // Build patch from current editor (normalized)
      const patch = getCopyPatchFromSelectedData();

      // Update local selectedGroupRow (preview card) — patch every sysPrin except the source
      if (typeof setSelectedGroupRow === 'function') {
        setSelectedGroupRow((prev) => {
          if (!prev?.sysPrins || !Array.isArray(prev.sysPrins)) return prev;

          const nextSysPrins = prev.sysPrins.map((sp) => {
            const spCode = sp?.id?.sysPrin ?? sp?.sysPrin;
            const spClient = sp?.id?.client ?? sp?.client ?? clientId;
            if (String(spCode) === String(source)) return sp; // skip source
            // merge patch and maintain id
            const merged = { ...sp, ...patch };
            merged.id = { client: spClient, sysPrin: spCode, ...(sp?.id ?? {}) };
            return merged;
          });

          return { ...(prev ?? {}), sysPrins: nextSysPrins };
        });
      }

      // Update the grid/list in the parent, one row per target
      if (typeof onPatchSysPrinsList === 'function' && Array.isArray(selectedGroupRow?.sysPrins)) {
        selectedGroupRow.sysPrins.forEach((sp) => {
          const spCode = sp?.id?.sysPrin ?? sp?.sysPrin;
          const spClient = sp?.id?.client ?? sp?.client ?? clientId;
          if (String(spCode) === String(source)) return; // skip source
          const merged = { ...sp, ...patch, id: { client: spClient, sysPrin: spCode, ...(sp?.id ?? {}) } };
          onPatchSysPrinsList(spCode, merged, spClient);
        });
      }

      alert('Copied settings to all Sys/Prins (except the source) successfully.');
    } catch (e) {
      console.error(e);
      alert(e?.message || 'Failed to Change All.');
    } finally {
      setSaving(false);
    }
  };

  // -------------------------------------------
  // On-change from tabs (focused updater)
  // -------------------------------------------
  const onChangeGeneral = (patchOrFn) => {
    setSelectedData((prev) => {
      const rawPatch = typeof patchOrFn === 'function' ? patchOrFn(prev) : (patchOrFn || {});
      const patch = Object.fromEntries(Object.entries(rawPatch).filter(([, v]) => v !== undefined));
      const next  = { ...(prev ?? {}), ...patch };

      const keySysPrin = patch.sysPrin ?? next.sysPrin ?? prev?.sysPrin ?? '';
      const keyClient  = patch.client  ?? next.client  ?? prev?.client  ?? undefined;

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
    'Create';

  const handlePrimaryClick = async () => {
    if (mode === 'new') {
      await handleSaveCreate();
    } else if (mode === 'duplicate') {
      await handleDuplicate();
    } else if (mode === 'move') {
      await handleMove();
    } else if (mode === 'changeAll') {
      await handleChangeAll();
    } else {
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

      {/* Move controls */}
      {mode === 'move' && (
        <Box sx={{ mt: 2, display: 'grid', gridTemplateColumns: '1fr 1fr', gap: 2 }}>
          <TextField
            size="small"
            label="Old Client ID"
            value={oldClientId}
            onChange={(e) => setOldClientId(e.target.value)}
            InputProps={{ sx: { fontSize: '0.85rem' } }}
          />
          <TextField
            size="small"
            label="New Client ID"
            value={newClientId}
            onChange={(e) => setNewClientId(e.target.value)}
            InputProps={{ sx: { fontSize: '0.85rem' } }}
          />
        </Box>
      )}

      {/* Duplicate controls */}
      {mode === 'duplicate' && (
        <Box sx={{ mt: 2, display: 'grid', gridTemplateColumns: '1fr', gap: 2 }}>
          <TextField
            size="small"
            label="Target SysPrin"
            placeholder="Enter target sys/prin code (e.g., 12121212)"
            value={targetSysPrin}
            onChange={(e) => setTargetSysPrin(e.target.value)}
            InputProps={{ sx: { fontSize: '0.85rem' } }}
          />
        </Box>
      )}

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
            onChangeGeneral={onChangeGeneral}
          />
        )}

        {tabIndex === 2 && (
          <EditStatusOptions
            selectedData={selectedData}
            statusMap={statusMap}
            setStatusMap={setStatusMap}
            isEditable={isEditable}
            onChangeGeneral={onChangeGeneral}
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
          <Button variant="contained" size="small" onClick={handlePrimaryClick} disabled={saving}>
            {saving
              ? (mode === 'changeAll' ? 'Applying…' :
                 mode === 'duplicate' ? 'Duplicating…' :
                 mode === 'move' ? 'Moving…' : 'Saving…')
              : (mode === 'changeAll' ? 'Change All' :
                 mode === 'duplicate' ? 'Duplicate' :
                 mode === 'move' ? 'Move' : 'Create')}
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
