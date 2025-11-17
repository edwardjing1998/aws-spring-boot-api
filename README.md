import React, { useState, useEffect, useMemo } from 'react';
import { Box, IconButton, Tabs, Tab, Button } from '@mui/material';
import CloseIcon from '@mui/icons-material/Close';
import { CRow, CCol } from '@coreui/react';

import EditModeButtonPanel from './EditModeButtonPanel';
import ChangeAllModeButtonPanel from './ChangeAllModeButtonPanel';
import CreateModeButtonPanel from './CreateModeButtonPanel';
import DuplicateModeButtonPanel from './DuplicateModeButtonPanel';
import MoveModeButtonPanel from './MoveModeButtonPanel';


import TextField from '@mui/material/TextField';

const titleByMode = {
  new: 'New Sys/Prin',
  edit: 'Edit Sys/Prin',
  duplicate: 'Duplicate Sys/Prin',
  move: 'Move Sys/Prin',
  changeAll: 'Change Sys/Prin',
};

  const getStatusValue = (options, code) => {
    const index = parseInt(code, 10);
    return !isNaN(index) ? options[index] || '' : '';
  };

  const sharedSx = {
    '& .MuiInputBase-root': {
      height: '20px',
      fontSize: '0.75rem'
    },
    '& .MuiInputBase-input': {
      padding: '4px 4px',
      height: '30px',
      fontSize: '0.75rem',
      lineHeight: '1rem'
    },
    '& .MuiInputLabel-root': {
      fontSize: '0.75rem',
      lineHeight: '1rem'
    },
    '& .MuiInputBase-input.Mui-disabled': {
      color: 'black',
      WebkitTextFillColor: 'black'
    },
    '& .MuiInputLabel-root.Mui-disabled': {
      color: 'black'
    }
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
  onPatchSysPrinsList, // bubble to the real owner of sysPrinsList
}) => {
  const [tabIndex, setTabIndex] = useState(0);
  const [isEditable, setIsEditable] = useState(true);
  const [statusMap, setStatusMap] = useState({});
  const [saving, setSaving] = useState(false);
  const [updating, setUpdating] = useState(false);

  const [sysPrinInput, setSysPrinInput] = useState(() => selectedData?.sysPrin ?? '');

  // Move-mode inputs
  const [oldClientId, setOldClientId] = useState(() => selectedGroupRow?.client ?? selectedData?.client ?? '');
  const [newClientId, setNewClientId] = useState('');

  // Duplicate-mode input
  const [targetSysPrin, setTargetSysPrin] = useState('');

  const primaryLabel =
  mode === 'changeAll' ? 'Change All'
  : mode === 'duplicate' ? 'Duplicate'
  : mode === 'move' ? 'Move'
  : mode === 'edit' ? 'Update'
  : mode === 'new' ? 'Create'
  : 'Create';

  useEffect(() => {
    setIsEditable(mode === 'edit' || mode === 'new');
  }, [mode]);

  useEffect(() => {
  setSysPrinInput(selectedData?.sysPrin ?? '');
}, [selectedData?.sysPrin]);


  const pushGeneralPatch = (patch) => {
    if (typeof onChangeGeneral === 'function') {
      const withKeys = {
        client: selectedData?.client,
        sysPrin: selectedData?.sysPrin,
        ...patch,
      };
//      handleNotesChange(withKeys);
    }
  };

  const maxTabs = 6;

  // -------------------------
  // Create payload (normalized)
  // -------------------------
  const buildCreatePayload = useMemo(() => {
    const sd = selectedData ?? {};

    const normalizeDestroy = (v) => (v == null ? ' ' : v); // keep as ' ' if backend expects blank

    return {
      // identity
      client:   selectedGroupRow?.client || '',
      sysPrin:  sd.sysPrin || '',

      // General tab
      custType:       sd.custType ?? '0',
      returnStatus:   sd.returnStatus ?? '',
      destroyStatus:  normalizeDestroy(sd.destroyStatus ?? '0'),
      special:        sd.special ?? '0',
      pinMailer:      sd.pinMailer ?? '0',
      sysPrinActive:  sd.sysPrinActive ?? '0',                // boolean for UI; backend may not need it
      rps:            String(sd.rps ?? '0'),       // decide later if your backend wants 'Y'/'N'
      addrFlag:       String(sd.addrFlag ?? '0'),
      astatRch:       String(sd.astatRch ?? '0'),
      nm13:           String(sd.nm13 ?? '0'),
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
      sysPrinContact:     sd.sysPrinContact ?? '',
      sysPrinPhone:       sd.sysPrinPhone ?? '',
      entityCode:         sd.entityCode ?? '0',
      session:            sd.session ?? '',
      badState:           sd.badState ?? '0',

      // Status options tab (KEEP AS-IS; do not coerce to '0'/'1')
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
  }, [selectedData, selectedGroupRow?.client]);

  // ---------------------------------------------------------
  // Build the patch we want to "copy to all" (Change All) API
  // ---------------------------------------------------------
  const getCopyPatchFromSelectedData = () => {
    const base = buildCreatePayload || {};

    // NEVER copy identity, optionally exclude slices
    const OMIT = new Set([
      'client',
      'sysPrin',
      'id',
      // 'vendorReceivedFrom',
      // 'vendorSentTo',
      // 'invalidDelivAreas',
      // 'notes',
    ]);

    // shallow copy without omitted fields
    const patch = Object.fromEntries(
      Object.entries(base).filter(([k]) => !OMIT.has(k))
    );

    // numeric fields
    if (patch.tempAway != null)                patch.tempAway                 = Number(patch.tempAway)                || 0;
    if (patch.tempAwayAtts != null)            patch.tempAwayAtts             = Number(patch.tempAwayAtts)            || 0;
    if (patch.reportMethod != null)            patch.reportMethod             = Number(patch.reportMethod)            || 0;
    if (patch.holdDays != null)                patch.holdDays                 = Number(patch.holdDays)                || 0;
    if (patch.nonUS != null)                   patch.nonUS                    = Number(patch.nonUS)                   || 0;
    if (patch.forwardingAddress != null)       patch.forwardingAddress        = Number(patch.forwardingAddress)       || 0;


    // binary flags that truly are 0/1 — DO NOT touch stat* here
    const ensure01 = (v) => (v === '1' || v === 1 || v === true) ? '1' : '0';
    ['addrFlag', 'astatRch', 'nm13', 'sysPrinActive'].forEach((k) => {
      if (k in patch) patch[k] = ensure01(patch[k]);
    });

    // rps handling — pick ONE format based on backend contract:
    if ('rps' in patch) {
      // patch.rps = ensure01(patch.rps);              // -> "0"/"1"
      // or:
      // patch.rps = (patch.rps === 'Y') ? 'Y' : 'N';  // -> "Y"/"N"
      // If your create payload is already "0"/"1", leave as-is:
      patch.rps = String(patch.rps ?? '0');
    }

    // Keep all stat* values EXACT (0..4). No coercion.

    // destroyStatus fallback
    if ('destroyStatus' in patch && (patch.destroyStatus == null || patch.destroyStatus === '')) {
      patch.destroyStatus = '0'; // or ' ' if backend uses a space for none
    }

    return patch;
  };

  // ----------------
  // CREATE (POST)
  // ----------------
  const handleSaveCreate = async () => {
    const client = selectedGroupRow?.client?.toString().trim();
    const sysPrin = selectedData?.sysPrin?.toString().trim();

    if (!client || !sysPrin) {
      alert('Client and SysPrin are required to create a new record.');
      return;
    }

    const url = `http://localhost:8089/client-sysprin-writer/api/sysprins/create/${encodeURIComponent(client)}/${encodeURIComponent(sysPrin)}`;
    const payload = buildCreatePayload; // object from useMemo

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
        } catch {}
        throw new Error(msg);
      }

      let created;
      try { created = await res.json(); } catch { created = null; }

      const canonical = created && typeof created === 'object' ? created : payload;
      canonical.client = client;
      canonical.sysPrin = sysPrin;

      const withId = { ...canonical, id: canonical.id ?? { client, sysPrin } };

      setSelectedData((prev) => ({ ...(prev ?? {}), ...withId }));

      if (typeof onPatchSysPrinsList === 'function') {
        onPatchSysPrinsList(sysPrin, withId, client);
      }

      if (typeof setSelectedGroupRow === 'function') {
        setSelectedGroupRow((prev) => {
          const prevId = prev?.id ?? {};
          return {
            ...(prev ?? {}),
            ...withId,
            id: { client, sysPrin, ...prevId },
            // also append into prev.sysPrins for the preview card
            sysPrins: Array.isArray(prev?.sysPrins)
              ? (() => {
                  const exists = prev.sysPrins.some(
                    (sp) => (sp?.id?.sysPrin ?? sp?.sysPrin) === sysPrin
                  );
                  return exists ? prev.sysPrins.map((sp) =>
                    (sp?.id?.sysPrin ?? sp?.sysPrin) === sysPrin ? { ...sp, ...withId } : sp
                  ) : [...prev.sysPrins, withId];
                })()
              : [withId],
          };
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

  // ------------
  // MOVE (PUT)
  // ------------
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
          if (ct.includes('application/json')) {
            const j = await res.json();
            msg = j?.message || JSON.stringify(j);
          } else {
            msg = await res.text();
          }
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
        // remove from old
        onPatchSysPrinsList(sysPrin, { __REMOVE__: true }, oldId);
        // add to new
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

  // ----------------
  // DUPLICATE (POST)
  // ----------------
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
          if (ct.includes('application/json')) {
            const j = await res.json();
            msg = j?.message || JSON.stringify(j);
          } else {
            msg = await res.text();
          }
        } catch {}
        throw new Error(msg);
      }

      let dup;
      try { dup = await res.json(); } catch { dup = null; }
      const canonical = (dup && typeof dup === 'object') ? dup : { ...(selectedData ?? {}) };
      canonical.client = clientId;
      canonical.sysPrin = target;
      const withId = { ...canonical, id: canonical.id ?? { client: clientId, sysPrin: target } };

      setSelectedData((prev) => ({ ...(prev ?? {}), ...withId }));

      if (typeof onPatchSysPrinsList === 'function') {
        onPatchSysPrinsList(target, withId, clientId);
      }

      if (typeof setSelectedGroupRow === 'function') {
        setSelectedGroupRow((prev) => {
          const prevId = prev?.id ?? {};
          return {
            ...(prev ?? {}),
            ...withId,
            id: { client: clientId, sysPrin: target, ...prevId },
            sysPrins: Array.isArray(prev?.sysPrins)
              ? (() => {
                  const exists = prev.sysPrins.some(
                    (sp) => (sp?.id?.sysPrin ?? sp?.sysPrin) === target
                  );
                  return exists ? prev.sysPrins.map((sp) =>
                    (sp?.id?.sysPrin ?? sp?.sysPrin) === target ? { ...sp, ...withId } : sp
                  ) : [...prev.sysPrins, withId];
                })()
              : [withId],
          };
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

  // ----------------
  // CHANGE ALL (POST)
  // ----------------
  // Calls: POST /api/clients/{clientId}/sysprins/copy-from/{sourceSysPrin}
  // Then locally patches all sysPrins (except source) in the parent list/card.
  const handleChangeAll = async () => {
    const clientId = (selectedGroupRow?.client ?? selectedData?.client ?? '').toString().trim();
    const sourceSysPrin = (selectedData?.sysPrin ?? '').toString().trim();

    if (!clientId || !sourceSysPrin) {
      alert('Client and Source SysPrin are required for Change All.');
      return;
    }

    const url =
      `http://localhost:8089/client-sysprin-writer/api/clients/${encodeURIComponent(clientId)}` +
      `/sysprins/copy-from/${encodeURIComponent(sourceSysPrin)}`;

    setSaving(true);
    try {
      const res = await fetch(url, { method: 'POST', headers: { accept: '*/*' }, body: '' });
      if (!res.ok) {
        let msg = `Change All failed (${res.status})`;
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

      // Server may return empty; compute patch from current editor state:
      const patch = getCopyPatchFromSelectedData();

      // 1) Update parent grid/canonical list for every target sysPrin under this client
      if (typeof onPatchSysPrinsList === 'function') {
        const sysPrins = Array.isArray(selectedGroupRow?.sysPrins) ? selectedGroupRow.sysPrins : [];
        sysPrins
          .map((sp) => sp?.id?.sysPrin ?? sp?.sysPrin)
          .filter((sp) => sp && sp !== sourceSysPrin)
          .forEach((target) => {
            onPatchSysPrinsList(target, { ...patch, id: { client: clientId, sysPrin: target } }, clientId);
          });
      }

      // 2) Patch preview card's sysPrins array (so user sees updates immediately)
      if (typeof setSelectedGroupRow === 'function') {
        setSelectedGroupRow((prev) => {
          if (!prev) return prev;
          const prevSysPrins = Array.isArray(prev.sysPrins) ? prev.sysPrins : [];
          const nextSysPrins = prevSysPrins.map((sp) => {
            const name = sp?.id?.sysPrin ?? sp?.sysPrin;
            if (name && name !== sourceSysPrin) {
              return { ...sp, ...patch, id: { client: clientId, sysPrin: name } };
            }
            return sp;
          });
          return { ...prev, sysPrins: nextSysPrins };
        });
      }

      alert('Change All completed successfully.');
    } catch (e) {
      console.error(e);
      alert(e?.message || 'Failed to Change All.');
    } finally {
      setSaving(false);
    }
  };

    const handleUpdate = async () => {
    const client = selectedData?.client;
    const sysPrinCode = selectedData?.sysPrin;
   
    if (!client || !sysPrinCode) {
      alert('Missing client or sysPrin.');
      return;
    }

    const url = `http://localhost:8089/client-sysprin-writer/api/sysprins/update/${encodeURIComponent(client)}/${encodeURIComponent(sysPrinCode)}`;

    setUpdating(true);
    try {
      const res = await fetch(url, {
        method: 'PUT',
        headers: { accept: '*/*', 'Content-Type': 'application/json' },
        body: JSON.stringify(buildCreatePayload),
      });

      if (!res.ok) {
        let msg = `Update failed (${res.status})`;
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

      // ✅ define `saved` safely (API may or may not return JSON)
      let saved = null;
      try {
        const ct = res.headers.get('Content-Type') || '';
        if (ct.includes('application/json')) {
          saved = await res.json();
        }
      } catch {}

      // Build the canonical patch for fields this page owns
      const patch = saved ?? {

        // Remail Options tab
        holdDays: buildPayload.holdDays,
        tempAway: buildPayload.tempAway,
        tempAwayAtts: buildPayload.tempAwayAtts,
        undeliverable: buildPayload.undeliverable,
        forwardingAddress: buildPayload.forwardingAddress,
        nonUS: buildPayload.nonUS,
        poBox: buildPayload.poBox,
        badState: buildPayload.badState,

        // General tab
        custType:       buildPayload.custType ?? '0',
        returnStatus:   buildPayload.returnStatus ?? '',
        destroyStatus:  normalizeDestroy(buildPayload.destroyStatus ?? '0'),
        special:        buildPayload.special ?? '0',
        pinMailer:      buildPayload.pinMailer ?? '0',
        sysPrinActive:  buildPayload.sysPrinActive ?? '0',               // boolean for UI; backend may not need it
        rps:            String(buildPayload.rps ?? '0'),       // decide later if your backend wants 'Y'/'N'
        addrFlag:       String(buildPayload.addrFlag ?? '0'),
        astatRch:       String(buildPayload.astatRch ?? '0'),
        nm13:           String(buildPayload.nm13 ?? '0'),
        notes:          buildPayload.notes ?? '',

        // Status options tab (KEEP AS-IS; do not coerce to '0'/'1')
        statA: String(buildPayload.statA ?? '0'),
        statB: String(buildPayload.statB ?? '0'),
        statC: String(buildPayload.statC ?? '0'),
        statD: String(buildPayload.statD ?? '0'),
        statE: String(buildPayload.statE ?? '0'),
        statF: String(buildPayload.statF ?? '0'),
        statI: String(buildPayload.statI ?? '0'),
        statL: String(buildPayload.statL ?? '0'),
        statO: String(buildPayload.statO ?? '0'),
        statU: String(buildPayload.statU ?? '0'),
        statX: String(buildPayload.statX ?? '0'),
        statZ: String(buildPayload.statZ ?? '0'),

        // no use
        reportMethod:       Number(buildPayload.reportMethod ?? 0) || 0,
        sysPrinContact:     buildPayload.sysPrinContact ?? '',
        sysPrinPhone:       buildPayload.sysPrinPhone ?? '',
        entityCode:         buildPayload.entityCode ?? '0',
        session:            buildPayload.session ?? '',
      };

      // Bubble up so parent list stays in sync
      pushGeneralPatch(patch);

      alert('SysPrin updated successfully.');
    } catch (e) {
      console.error(e);
      alert(e?.message || 'Failed to update.');
    } finally {
      setUpdating(false);
    }
  };

  // -----------------------
  // Focused field updater
  // -----------------------
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

      if (typeof onPatchSysPrinsList === 'function' && keySysPrin) {
        onPatchSysPrinsList(keySysPrin, patch, keyClient);
      }

      return next;
    });
  };

  // ----------
  // Primary CTA
  // ----------
  const handlePrimaryClick = async () => {
    if (mode === 'new') {
      await handleSaveCreate();
    } else if (mode === 'duplicate') {
      await handleDuplicate();
    } else if (mode === 'move') {
      await handleMove();
    } else if (mode === 'changeAll') {
      await handleChangeAll();
    } else if (mode === 'edit'){
      await handleUpdate();
    } else {
      alert('Not in a supported mode.');
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

      {/* SysPrin input (serves as "copy-from" sysprin in Change All) */}
      <Box
        sx={{
          display: 'flex',
          justifyContent: 'space-between',
          alignItems: 'center',
          mt: '20px',
          backgroundColor: '#f3f6f8',
          height: '50px',
          width: '100%',
          px: 2,
          gap: 2,
        }}
      >
        <input
          type="text"
          placeholder="Enter Sys/Prin Name"
          value={sysPrinInput}
          onChange={(e) => {
            setSysPrinInput(e.target.value);   // only update local state → smooth typing
          }}
          onBlur={() => {
            // when user leaves the field, sync to selectedData once
            setSelectedData((prev) => ({
              ...(prev ?? {}),
              sysPrin: sysPrinInput,
            }));
          }}
          disabled={!isEditable && mode === 'changeAll'}
          style={{
            fontSize: '0.9rem',
            fontWeight: 400,
            width: '30vw',
            height: '30px',
            border: '1px solid #ccc',
            borderRadius: '4px',
            paddingLeft: '8px',
            backgroundColor: 'white',
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

      {mode === 'edit' && (      
      <EditModeButtonPanel
        mode={mode}
        tabIndex={tabIndex}
        setTabIndex={setTabIndex}
        selectedData={selectedData}
        setSelectedData={setSelectedData}
        isEditable={isEditable}
        onChangeGeneral={onChangeGeneral}
        statusMap={statusMap}
        setStatusMap={setStatusMap}
        onChangeVendorReceivedFrom={onChangeVendorReceivedFrom}
        onChangeVendorSentTo={onChangeVendorSentTo}
        saving={saving}
        primaryLabel={primaryLabel}
        sharedSx={sharedSx}
        getStatusValue={getStatusValue}
        handlePrimaryClick={handlePrimaryClick}
      />
    )}

    {mode === 'new' && (      
      <CreateModeButtonPanel
        mode={mode}
        tabIndex={tabIndex}
        setTabIndex={setTabIndex}
        selectedData={selectedData}
        setSelectedData={setSelectedData}
        isEditable={isEditable}
        onChangeGeneral={onChangeGeneral}
        statusMap={statusMap}
        setStatusMap={setStatusMap}
        onChangeVendorReceivedFrom={onChangeVendorReceivedFrom}
        onChangeVendorSentTo={onChangeVendorSentTo}
        saving={saving}
        primaryLabel={primaryLabel}
        sharedSx={sharedSx}
        getStatusValue={getStatusValue}
        handlePrimaryClick={handlePrimaryClick}
      />
    )}

    {mode === 'duplicate' && (      
      <DuplicateModeButtonPanel
        mode={mode}
        tabIndex={tabIndex}
        setTabIndex={setTabIndex}
        selectedData={selectedData}
        setSelectedData={setSelectedData}
        isEditable={isEditable}
        onChangeGeneral={onChangeGeneral}
        statusMap={statusMap}
        setStatusMap={setStatusMap}
        onChangeVendorReceivedFrom={onChangeVendorReceivedFrom}
        onChangeVendorSentTo={onChangeVendorSentTo}
        saving={saving}
        primaryLabel={primaryLabel}
        sharedSx={sharedSx}
        getStatusValue={getStatusValue}
        handlePrimaryClick={handlePrimaryClick}
      />
    )}

    {mode === 'changeAll' && (      
      <ChangeAllModeButtonPanel
        mode={mode}
        tabIndex={tabIndex}
        setTabIndex={setTabIndex}
        selectedData={selectedData}
        setSelectedData={setSelectedData}
        isEditable={isEditable}
        onChangeGeneral={onChangeGeneral}
        statusMap={statusMap}
        setStatusMap={setStatusMap}
        onChangeVendorReceivedFrom={onChangeVendorReceivedFrom}
        onChangeVendorSentTo={onChangeVendorSentTo}
        saving={saving}
        primaryLabel={primaryLabel}
        sharedSx={sharedSx}
        getStatusValue={getStatusValue}
        handlePrimaryClick={handlePrimaryClick}
      />
    )}

   {mode === 'move' && (      
      <MoveModeButtonPanel
        mode={mode}
        tabIndex={tabIndex}
        setTabIndex={setTabIndex}
        selectedData={selectedData}
        setSelectedData={setSelectedData}
        isEditable={isEditable}
        onChangeGeneral={onChangeGeneral}
        statusMap={statusMap}
        setStatusMap={setStatusMap}
        onChangeVendorReceivedFrom={onChangeVendorReceivedFrom}
        onChangeVendorSentTo={onChangeVendorSentTo}
        saving={saving}
        primaryLabel={primaryLabel}
        sharedSx={sharedSx}
        getStatusValue={getStatusValue}
        handlePrimaryClick={handlePrimaryClick}
      />
    )}
    </Box>
  );
};

export default SysPrinInformationWindow;
