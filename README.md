import React, { useState, useEffect, useMemo } from 'react';
import { Box, IconButton } from '@mui/material';
import CloseIcon from '@mui/icons-material/Close';
import TextField from '@mui/material/TextField';

import EditModeButtonPanel from './EditModeButtonPanel';
import ChangeAllModeButtonPanel from './ChangeAllModeButtonPanel';
import CreateModeButtonPanel from './CreateModeButtonPanel';
import DuplicateModeButtonPanel from './DuplicateModeButtonPanel';
import MoveModeButtonPanel from './MoveModeButtonPanel';
import DeleteModeButtonPanel from './DeleteModeButtonPanel';

// ---- Types ----
type SysPrinMode = 'new' | 'edit' | 'delete' | 'duplicate' | 'move' | 'changeAll';

interface SysPrinInformationWindowProps {
  onClose: () => void;
  mode: SysPrinMode;
  selectedData: any;
  setSelectedData: React.Dispatch<React.SetStateAction<any>>;
  selectedGroupRow: any;
  setSelectedGroupRow: React.Dispatch<React.SetStateAction<any>>;
  // existing focused updaters
  onChangeVendorReceivedFrom: (nextList: any[]) => void;
  onChangeVendorSentTo: (nextList: any[]) => void;
  onPatchSysPrinsList: (sysPrin: string, patch: any, clientId?: string) => void;
}

// shared helper (your original code called normalizeDestroy in multiple places)
const normalizeDestroy = (v: any): string => {
  return v == null ? ' ' : String(v);
};

const titleByMode: Record<SysPrinMode, string> = {
  new: 'New Sys/Prin',
  edit: 'Edit Sys/Prin',
  delete: 'Delete Sys/Prin',
  duplicate: 'Duplicate Sys/Prin',
  move: 'Move Sys/Prin',
  changeAll: 'Change Sys/Prin',
};

const getStatusValue = (options: string[], code: string | number | undefined): string => {
  if (code === undefined || code === null) return '';
  const index = parseInt(String(code), 10);
  return !isNaN(index) ? options[index] || '' : '';
};

const sharedSx = {
  '& .MuiInputBase-root': {
    height: '20px',
    fontSize: '0.75rem',
  },
  '& .MuiInputBase-input': {
    padding: '4px 4px',
    height: '30px',
    fontSize: '0.75rem',
    lineHeight: '1rem',
  },
  '& .MuiInputLabel-root': {
    fontSize: '0.75rem',
    lineHeight: '1rem',
  },
  '& .MuiInputBase-input.Mui-disabled': {
    color: 'black',
    WebkitTextFillColor: 'black',
  },
  '& .MuiInputLabel-root.Mui-disabled': {
    color: 'black',
  },
};

const SysPrinInformationWindow: React.FC<SysPrinInformationWindowProps> = ({
  onClose,
  mode,
  selectedData,
  setSelectedData,
  selectedGroupRow,
  setSelectedGroupRow,
  onChangeVendorReceivedFrom,
  onChangeVendorSentTo,
  onPatchSysPrinsList,
}) => {
  const [tabIndex, setTabIndex] = useState<number>(0);
  const [isEditable, setIsEditable] = useState<boolean>(true);
  const [statusMap, setStatusMap] = useState<Record<string, any>>({});
  const [saving, setSaving] = useState<boolean>(false);
  const [updating, setUpdating] = useState<boolean>(false);

  const fieldSx = {
    '& .MuiInputBase-root': {
      height: 32,
      fontSize: '0.85rem',
    },
    '& .MuiInputBase-input': {
      padding: '4px 8px',
      fontSize: '0.85rem',
    },
    '& .MuiInputLabel-root': {
      fontSize: '0.78rem',
    },
  };

  const [sysPrinInput, setSysPrinInput] = useState<string>(() => selectedData?.sysPrin ?? '');

  // Move-mode inputs
  const [oldClientId, setOldClientId] = useState<string>(
    () => selectedGroupRow?.client ?? selectedData?.client ?? '',
  );
  const [newClientId, setNewClientId] = useState<string>('');

  // Duplicate-mode input
  const [targetSysPrin, setTargetSysPrin] = useState<string>('');

  const primaryLabel =
    mode === 'changeAll'
      ? 'Change All'
      : mode === 'duplicate'
      ? 'Duplicate'
      : mode === 'move'
      ? 'Move'
      : mode === 'edit'
      ? 'Update'
      : mode === 'new'
      ? 'Create'
      : mode === 'delete'
      ? 'Delete'
      : 'Create';

  useEffect(() => {
    setIsEditable(mode === 'edit' || mode === 'new');
  }, [mode]);

  useEffect(() => {
    setSysPrinInput(selectedData?.sysPrin ?? '');
  }, [selectedData?.sysPrin]);

  // -----------------------
  // Focused field updater
  // -----------------------
  const onChangeGeneral = (patchOrFn: any) => {
    setSelectedData((prev: any) => {
      const rawPatch =
        typeof patchOrFn === 'function' ? patchOrFn(prev) : patchOrFn || {};
      const patch = Object.fromEntries(
        Object.entries(rawPatch).filter(([, v]) => v !== undefined),
      );
      const next: any = { ...(prev ?? {}), ...patch };

      const keySysPrin =
        patch.sysPrin ?? next.sysPrin ?? prev?.sysPrin ?? '';
      const keyClient =
        patch.client ?? next.client ?? prev?.client ?? undefined;

      // keep your local shadow list in selectedData (so the window reflects instantly)
      const listFromPrev = Array.isArray(prev?.sysPrins)
        ? prev.sysPrins
        : [];
      const listFromNext = Array.isArray(next?.sysPrins)
        ? next.sysPrins
        : [];
      const list = [...(listFromPrev.length ? listFromPrev : listFromNext)];

      const matchFn = (sp: any) => {
        const spCode = sp?.id?.sysPrin ?? sp?.sysPrin;
        const spClient = sp?.id?.client ?? sp?.client;
        if (!spCode) return false;
        return keyClient != null
          ? spCode === keySysPrin && spClient === keyClient
          : spCode === keySysPrin;
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
        const sgCode = sg?.id?.sysPrin ?? sg?.sysPrin;
        const sgClient = sg?.id?.client ?? sg?.client;
        if (
          sgCode === keySysPrin &&
          (keyClient != null ? sgClient === keyClient : true)
        ) {
          next.selectedGroupRow = { ...sg, ...patch };
        }
      }

      if (typeof onPatchSysPrinsList === 'function' && keySysPrin) {
        onPatchSysPrinsList(keySysPrin, patch, keyClient);
      }

      return next;
    });
  };

  const pushGeneralPatch = (patch: any) => {
    const withKeys = {
      client: selectedData?.client,
      sysPrin: selectedData?.sysPrin,
      ...(patch || {}),
    };

    // Prefer the shared updater so it also syncs preview + parent list
    if (typeof onChangeGeneral === 'function') {
      onChangeGeneral(withKeys);
    } else {
      // Fallback: at least keep selectedData in sync
      setSelectedData((prev: any) => ({
        ...(prev ?? {}),
        ...withKeys,
      }));
    }
  };

  // -------------------------
  // Create payload (normalized)
  // -------------------------
  const buildCreatePayload = useMemo(() => {
    const sd = selectedData ?? {};

    return {
      // identity
      client: selectedGroupRow?.client || '',
      sysPrin: sd.sysPrin || '',

      // General tab
      custType: sd.custType ?? '0',
      returnStatus: sd.returnStatus ?? '',
      destroyStatus: normalizeDestroy(sd.destroyStatus ?? '0'),
      special: sd.special ?? '0',
      pinMailer: sd.pinMailer ?? '0',
      sysPrinActive: sd.sysPrinActive ?? '0',
      rps: String(sd.rps ?? '0'),
      addrFlag: String(sd.addrFlag ?? '0'),
      astatRch: String(sd.astatRch ?? '0'),
      nm13: String(sd.nm13 ?? '0'),
      notes: sd.notes ?? '',

      // ReMail options tab
      undeliverable: sd.undeliverable ?? '0',
      poBox: sd.poBox ?? '0',
      tempAway: Number(sd.tempAway ?? 0) || 0,
      tempAwayAtts: Number(sd.tempAwayAtts ?? 0) || 0,
      reportMethod: Number(sd.reportMethod ?? 0) || 0,
      nonUS: sd.nonUS ?? '0',
      holdDays: Number(sd.holdDays ?? 0) || 0,
      forwardingAddress: sd.forwardingAddress ?? '0',
      sysPrinContact: sd.sysPrinContact ?? '',
      sysPrinPhone: sd.sysPrinPhone ?? '',
      entityCode: sd.entityCode ?? '0',
      session: sd.session ?? '',
      badState: sd.badState ?? '0',

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
  }, [selectedData, selectedGroupRow?.client]);

  // ---------------------------------------------------------
  // Build the patch we want to "copy to all" (Change All) API
  // ---------------------------------------------------------
  const getCopyPatchFromSelectedData = () => {
    const base: any = buildCreatePayload || {};

    const OMIT = new Set([
      'client',
      'sysPrin',
      'id',
      // 'vendorReceivedFrom',
      // 'vendorSentTo',
      // 'invalidDelivAreas',
      // 'notes',
    ]);

    const patch: any = Object.fromEntries(
      Object.entries(base).filter(([k]) => !OMIT.has(k)),
    );

    // numeric fields
    if (patch.tempAway != null)
      patch.tempAway = Number(patch.tempAway) || 0;
    if (patch.tempAwayAtts != null)
      patch.tempAwayAtts = Number(patch.tempAwayAtts) || 0;
    if (patch.reportMethod != null)
      patch.reportMethod = Number(patch.reportMethod) || 0;
    if (patch.holdDays != null)
      patch.holdDays = Number(patch.holdDays) || 0;
    if (patch.nonUS != null)
      patch.nonUS = Number(patch.nonUS) || 0;
    if (patch.forwardingAddress != null)
      patch.forwardingAddress = Number(patch.forwardingAddress) || 0;

    const ensure01 = (v: any) =>
      v === '1' || v === 1 || v === true ? '1' : '0';
    ['addrFlag', 'astatRch', 'nm13', 'sysPrinActive'].forEach((k) => {
      if (k in patch) patch[k] = ensure01(patch[k]);
    });

    if ('rps' in patch) {
      patch.rps = String(patch.rps ?? '0');
    }

    if (
      'destroyStatus' in patch &&
      (patch.destroyStatus == null || patch.destroyStatus === '')
    ) {
      patch.destroyStatus = '0';
    }

    return patch;
  };

  // Collect area/vendor slices that should also be copied on "Change All"
  const getSourceVendorPatch = () => {
    const sd = selectedData ?? {};
    return {
      vendorReceivedFrom: Array.isArray(sd.vendorReceivedFrom)
        ? sd.vendorReceivedFrom
        : [],
      vendorSentTo: Array.isArray(sd.vendorSentTo)
        ? sd.vendorSentTo
        : [],
      invalidDelivAreas: Array.isArray(sd.invalidDelivAreas)
        ? sd.invalidDelivAreas
        : [],
      // notes: sd.notes ?? '',
    };
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

    const url = `http://localhost:8089/client-sysprin-writer/api/sysprins/create/${encodeURIComponent(
      client,
    )}/${encodeURIComponent(sysPrin)}`;
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
            msg = (j as any)?.message || JSON.stringify(j);
          } else {
            msg = await res.text();
          }
        } catch {}
        throw new Error(msg);
      }

      let created: any = null;
      try {
        created = await res.json();
      } catch {
        created = null;
      }

      const canonical: any =
        created && typeof created === 'object' ? created : payload;
      canonical.client = client;
      canonical.sysPrin = sysPrin;

      const withId = {
        ...canonical,
        id: canonical.id ?? { client, sysPrin },
      };

      setSelectedData((prev: any) => ({ ...(prev ?? {}), ...withId }));

      if (typeof onPatchSysPrinsList === 'function') {
        onPatchSysPrinsList(sysPrin, withId, client);
      }

      if (typeof setSelectedGroupRow === 'function') {
        setSelectedGroupRow((prev: any) => {
          const prevId = prev?.id ?? {};
          return {
            ...(prev ?? {}),
            ...withId,
            id: { client, sysPrin, ...prevId },
            sysPrins: Array.isArray(prev?.sysPrins)
              ? (() => {
                  const exists = prev.sysPrins.some(
                    (sp: any) =>
                      (sp?.id?.sysPrin ?? sp?.sysPrin) === sysPrin,
                  );
                  return exists
                    ? prev.sysPrins.map((sp: any) =>
                        (sp?.id?.sysPrin ?? sp?.sysPrin) === sysPrin
                          ? { ...sp, ...withId }
                          : sp,
                      )
                    : [...prev.sysPrins, withId];
                })()
              : [withId],
          };
        });
      }

      alert('Sys/Prin created successfully.');
    } catch (e: any) {
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
    const sysPrinCode = (
      sysPrinInput ||
      selectedData?.sysPrin ||
      ''
    )
      .toString()
      .trim();

    const oldId = (
      oldClientId ||
      selectedData?.client ||
      selectedGroupRow?.client ||
      ''
    )
      .toString()
      .trim();

    const newId = (newClientId || '').toString().trim();

    if (!sysPrinCode || !oldId || !newId) {
      alert(
        'Old Client ID, New Client ID, and SysPrin are required.',
      );
      return;
    }

    const url =
      `http://localhost:8089/client-sysprin-writer/api/sysprins/move-sysprin` +
      `?oldClientId=${encodeURIComponent(oldId)}` +
      `&sysPrin=${encodeURIComponent(sysPrinCode)}` +
      `&newClientId=${encodeURIComponent(newId)}`;

    setSaving(true);
    try {
      const res = await fetch(url, {
        method: 'PUT',
        headers: { accept: '*/*' },
      });
      if (!res.ok) {
        let msg = `Move failed (${res.status})`;
        try {
          const ct = res.headers.get('Content-Type') || '';
          if (ct.includes('application/json')) {
            const j = await res.json();
            msg = (j as any)?.message || JSON.stringify(j);
          } else {
            msg = await res.text();
          }
        } catch {}
        throw new Error(msg);
      }

      const moved: any = {
        ...(selectedData ?? {}),
        client: newId,
        sysPrin: sysPrinCode,
        id: { client: newId, sysPrin: sysPrinCode },
      };

      setSelectedData((prev: any) => ({ ...(prev ?? {}), ...moved }));

      if (typeof onPatchSysPrinsList === 'function') {
        onPatchSysPrinsList(sysPrinCode, { __REMOVE__: true }, oldId);
        onPatchSysPrinsList(sysPrinCode, moved, newId);
      }

      if (typeof setSelectedGroupRow === 'function') {
        setSelectedGroupRow((prev: any) => {
          if (!prev) return prev;
          const prevSysPrins = Array.isArray(prev.sysPrins)
            ? prev.sysPrins
            : [];

          const nextSysPrins = prevSysPrins.filter((sp: any) => {
            const code = sp?.id?.sysPrin ?? sp?.sysPrin;
            const client = sp?.id?.client ?? sp?.client;
            return !(code === sysPrinCode && client === oldId);
          });

          return { ...prev, sysPrins: nextSysPrins };
        });
      }

      alert('Sys/Prin moved successfully.');
    } catch (e: any) {
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
    const clientId = (
      selectedGroupRow?.client ??
      selectedData?.client ??
      ''
    )
      .toString()
      .trim();
    const source = (selectedData?.sysPrin ?? '')
      .toString()
      .trim();
    const target = (targetSysPrin ?? '').toString().trim();

    if (!clientId || !source || !target) {
      alert(
        'Client, Source SysPrin, and Target SysPrin are required.',
      );
      return;
    }
    if (source === target) {
      alert(
        'Target SysPrin must be different from Source SysPrin.',
      );
      return;
    }

    const url =
      `http://localhost:8089/client-sysprin-writer/api/clients/${encodeURIComponent(
        clientId,
      )}` +
      `/sysprins/${encodeURIComponent(source)}` +
      `/duplicate-to/${encodeURIComponent(target)}` +
      `?overwrite=false&copyAreas=true`;

    setSaving(true);
    try {
      const res = await fetch(url, {
        method: 'POST',
        headers: { accept: '*/*' },
        body: '',
      });

      if (!res.ok) {
        let msg = `Duplicate failed (${res.status})`;
        try {
          const ct = res.headers.get('Content-Type') || '';
          if (ct.includes('application/json')) {
            const j = await res.json();
            msg = (j as any)?.message || JSON.stringify(j);
          } else {
            msg = await res.text();
          }
        } catch {}
        throw new Error(msg);
      }

      const scalarPatch = getCopyPatchFromSelectedData();
      const vendorPatch = getSourceVendorPatch();

      const fullPatch: any = {
        ...scalarPatch,
        ...vendorPatch,
        client: clientId,
        sysPrin: target,
        id: { client: clientId, sysPrin: target },
      };

      setSelectedData((prev: any) => ({
        ...(prev ?? {}),
        ...fullPatch,
      }));

      if (typeof onPatchSysPrinsList === 'function') {
        onPatchSysPrinsList(target, fullPatch, clientId);
      }

      if (typeof setSelectedGroupRow === 'function') {
        setSelectedGroupRow((prev: any) => {
          if (!prev) return prev;

          const prevSysPrins = Array.isArray(prev.sysPrins)
            ? prev.sysPrins
            : [];

          const exists = prevSysPrins.some((sp: any) => {
            const code = sp?.id?.sysPrin ?? sp?.sysPrin;
            const client = sp?.id?.client ?? sp?.client;
            return code === target && client === clientId;
          });

          const nextSysPrins = exists
            ? prevSysPrins.map((sp: any) => {
                const code = sp?.id?.sysPrin ?? sp?.sysPrin;
                const client = sp?.id?.client ?? sp?.client;
                if (code === target && client === clientId) {
                  return { ...sp, ...fullPatch };
                }
                return sp;
              })
            : [...prevSysPrins, fullPatch];

          return { ...prev, sysPrins: nextSysPrins };
        });
      }

      alert('Sys/Prin duplicated successfully.');
    } catch (e: any) {
      console.error(e);
      alert(e?.message || 'Failed to duplicate Sys/Prin.');
    } finally {
      setSaving(false);
    }
  };

  // ----------------
  // CHANGE ALL (POST)
  // ----------------
  const handleChangeAll = async () => {
    const clientId = (
      selectedGroupRow?.client ??
      selectedData?.client ??
      ''
    )
      .toString()
      .trim();
    const sourceSysPrin = (selectedData?.sysPrin ?? '')
      .toString()
      .trim();

    if (!clientId || !sourceSysPrin) {
      alert('Client and Source SysPrin are required for Change All.');
      return;
    }

    const url =
      `http://localhost:8089/client-sysprin-writer/api/clients/${encodeURIComponent(
        clientId,
      )}` + `/sysprins/copy-from/${encodeURIComponent(sourceSysPrin)}`;

    setSaving(true);
    try {
      const res = await fetch(url, {
        method: 'POST',
        headers: { accept: '*/*' },
        body: '',
      });
      if (!res.ok) {
        let msg = `Change All failed (${res.status})`;
        try {
          const ct = res.headers.get('Content-Type') || '';
          if (ct.includes('application/json')) {
            const j = await res.json();
            msg = (j as any)?.message || JSON.stringify(j);
          } else {
            msg = await res.text();
          }
        } catch {}
        throw new Error(msg);
      }

      const scalarPatch = getCopyPatchFromSelectedData();
      const vendorPatch = getSourceVendorPatch();
      const fullPatch: any = { ...scalarPatch, ...vendorPatch };

      if (typeof onPatchSysPrinsList === 'function') {
        const sysPrins = Array.isArray(selectedGroupRow?.sysPrins)
          ? selectedGroupRow.sysPrins
          : [];
        sysPrins
          .map((sp: any) => sp?.id?.sysPrin ?? sp?.sysPrin)
          .filter((sp: any) => sp && sp !== sourceSysPrin)
          .forEach((target: string) => {
            onPatchSysPrinsList(
              target,
              { ...fullPatch, id: { client: clientId, sysPrin: target } },
              clientId,
            );
          });
      }

      if (typeof setSelectedGroupRow === 'function') {
        setSelectedGroupRow((prev: any) => {
          if (!prev) return prev;
          const prevSysPrins = Array.isArray(prev.sysPrins)
            ? prev.sysPrins
            : [];
          const nextSysPrins = prevSysPrins.map((sp: any) => {
            const name = sp?.id?.sysPrin ?? sp?.sysPrin;
            if (name && name !== sourceSysPrin) {
              return {
                ...sp,
                ...fullPatch,
                id: { client: clientId, sysPrin: name },
              };
            }
            return sp;
          });
          return { ...prev, sysPrins: nextSysPrins };
        });
      }

      alert('Change All completed successfully.');
    } catch (e: any) {
      console.error(e);
      alert(e?.message || 'Failed to Change All.');
    } finally {
      setSaving(false);
    }
  };

  // --------------
  // DELETE (DELETE)
  // --------------
  const handleDeleteSysPrin = async () => {
    const clientId = (
      selectedGroupRow?.client ??
      selectedData?.client ??
      ''
    )
      .toString()
      .trim();
    const sysPrinCode = (selectedData?.sysPrin ?? '')
      .toString()
      .trim();

    if (!clientId || !sysPrinCode) {
      alert('Client and SysPrin are required to delete.');
      return;
    }

    if (
      !window.confirm(
        `Are you sure you want to delete Sys/Prin "${sysPrinCode}" for client "${clientId}"?`,
      )
    ) {
      return;
    }

    const url =
      `http://localhost:8089/client-sysprin-writer/api/sysprins/${encodeURIComponent(
        clientId,
      )}/${encodeURIComponent(sysPrinCode)}`;

    setSaving(true);
    try {
      const res = await fetch(url, {
        method: 'DELETE',
        headers: { accept: '*/*' },
      });
      if (!res.ok) {
        let msg = `Delete Sys/Prin failed (${res.status})`;
        try {
          const ct = res.headers.get('Content-Type') || '';
          if (ct.includes('application/json')) {
            const j = await res.json();
            msg = (j as any)?.message || JSON.stringify(j);
          } else {
            msg = await res.text();
          }
        } catch {}
        throw new Error(msg);
      }

      if (typeof onPatchSysPrinsList === 'function') {
        onPatchSysPrinsList(sysPrinCode, { __REMOVE__: true }, clientId);
      }

      if (typeof setSelectedGroupRow === 'function') {
        setSelectedGroupRow((prev: any) => {
          if (!prev) return prev;
          const prevSysPrins = Array.isArray(prev.sysPrins)
            ? prev.sysPrins
            : [];
          const nextSysPrins = prevSysPrins.filter((sp: any) => {
            const code = sp?.id?.sysPrin ?? sp?.sysPrin;
            const client = sp?.id?.client ?? sp?.client;
            return !(code === sysPrinCode && client === clientId);
          });
          return { ...prev, sysPrins: nextSysPrins };
        });
      }

      setSelectedData((prev: any) => {
        if (!prev) return prev;
        const code = prev?.id?.sysPrin ?? prev?.sysPrin;
        const client = prev?.id?.client ?? prev?.client;
        if (code === sysPrinCode && client === clientId) {
          return null;
        }
        return prev;
      });

      alert('Delete Sys/Prin completed successfully.');
    } catch (e: any) {
      console.error(e);
      alert(e?.message || 'Failed to delete Sys/Prin.');
    } finally {
      setSaving(false);
    }
  };

  // ------------
  // UPDATE (PUT)
  // ------------
  const handleUpdate = async () => {
    const client = selectedData?.client;
    const sysPrinCode = selectedData?.sysPrin;

    if (!client || !sysPrinCode) {
      alert('Missing client or sysPrin.');
      return;
    }

    const url = `http://localhost:8089/client-sysprin-writer/api/sysprins/update/${encodeURIComponent(
      client,
    )}/${encodeURIComponent(sysPrinCode)}`;

    const payload: any = buildCreatePayload;

    setUpdating(true);
    try {
      const res = await fetch(url, {
        method: 'PUT',
        headers: { accept: '*/*', 'Content-Type': 'application/json' },
        body: JSON.stringify(payload),
      });

      if (!res.ok) {
        let msg = `Update failed (${res.status})`;
        try {
          const ct = res.headers.get('Content-Type') || '';
          if (ct.includes('application/json')) {
            const j = await res.json();
            msg = (j as any)?.message || JSON.stringify(j);
          } else {
            msg = await res.text();
          }
        } catch {}
        throw new Error(msg);
      }

      let saved: any = null;
      try {
        const ct = res.headers.get('Content-Type') || '';
        if (ct.includes('application/json')) {
          saved = await res.json();
        }
      } catch {}

      const patch: any =
        saved ??
        {
          holdDays: payload.holdDays,
          tempAway: payload.tempAway,
          tempAwayAtts: payload.tempAwayAtts,
          undeliverable: payload.undeliverable,
          forwardingAddress: payload.forwardingAddress,
          nonUS: payload.nonUS,
          poBox: payload.poBox,
          badState: payload.badState,

          custType: payload.custType ?? '0',
          returnStatus: payload.returnStatus ?? '',
          destroyStatus: normalizeDestroy(payload.destroyStatus ?? '0'),
          special: payload.special ?? '0',
          pinMailer: payload.pinMailer ?? '0',
          sysPrinActive: payload.sysPrinActive ?? '0',
          rps: String(payload.rps ?? '0'),
          addrFlag: String(payload.addrFlag ?? '0'),
          astatRch: String(payload.astatRch ?? '0'),
          nm13: String(payload.nm13 ?? '0'),
          notes: payload.notes ?? '',

          statA: String(payload.statA ?? '0'),
          statB: String(payload.statB ?? '0'),
          statC: String(payload.statC ?? '0'),
          statD: String(payload.statD ?? '0'),
          statE: String(payload.statE ?? '0'),
          statF: String(payload.statF ?? '0'),
          statI: String(payload.statI ?? '0'),
          statL: String(payload.statL ?? '0'),
          statO: String(payload.statO ?? '0'),
          statU: String(payload.statU ?? '0'),
          statX: String(payload.statX ?? '0'),
          statZ: String(payload.statZ ?? '0'),

          reportMethod: Number(payload.reportMethod ?? 0) || 0,
          sysPrinContact: payload.sysPrinContact ?? '',
          sysPrinPhone: payload.sysPrinPhone ?? '',
          entityCode: payload.entityCode ?? '0',
          session: payload.session ?? '',
        };

      pushGeneralPatch(patch);

      alert('SysPrin updated successfully.');
    } catch (e: any) {
      console.error(e);
      alert(e?.message || 'Failed to update.');
    } finally {
      setUpdating(false);
    }
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
    } else if (mode === 'delete') {
      await handleDeleteSysPrin();
    } else if (mode === 'edit') {
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
          display: 'flex',
          justifyContent: 'space-between',
          alignItems: 'center',
          bgcolor: '#1976d2',
          color: '#fff',
          px: 2,
          py: 1,
          mx: -4,
          mt: -4,
          borderRadius: 0,
        }}
      >
        <span style={{ fontSize: '0.9rem', fontWeight: 500 }}>
          {titleByMode[mode] ?? 'Sys/Prin'}
        </span>
        <IconButton onClick={onClose} size="small" sx={{ color: '#fff' }}>
          <CloseIcon fontSize="small" />
        </IconButton>
      </Box>

      {/* SysPrin input */}
      {(mode === 'edit' ||
        mode === 'new' ||
        mode === 'duplicate' ||
        mode === 'changeAll' ||
        mode === 'delete') && (
        <Box
          sx={{
            display: 'flex',
            justifyContent: 'space-between',
            alignItems: 'center',
            mt: '20px',
            backgroundColor: '#f3f6f8',
            height: '50px',
            width: '30%',
            px: 2,
            gap: 2,
          }}
        >
          <input
            type="text"
            placeholder="Enter Sys/Prin ID"
            value={sysPrinInput}
            onChange={(e) => {
              setSysPrinInput(e.target.value);
            }}
            onBlur={() => {
              setSelectedData((prev: any) => ({
                ...(prev ?? {}),
                sysPrin: sysPrinInput,
              }));
            }}
            disabled={!isEditable && (mode === 'changeAll' || mode === 'delete')}
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
      )}

      {/* Move controls */}
      {mode === 'move' && (
        <Box
          sx={{
            mt: 2,
            display: 'grid',
            gridTemplateColumns: 'repeat(3, 1fr)',
            gap: 2,
            alignItems: 'center',
          }}
        >
          <TextField
            fullWidth
            size="small"
            label="Sys/Prin ID"
            placeholder="Enter Sys/Prin ID"
            value={sysPrinInput}
            onChange={(e) => {
              setSysPrinInput(e.target.value);
            }}
            onBlur={() => {
              setSelectedData((prev: any) => ({
                ...(prev ?? {}),
                sysPrin: sysPrinInput,
              }));
            }}
            disabled={!isEditable && (mode === 'changeAll' || mode === 'delete')}
            sx={fieldSx}
          />

          <TextField
            fullWidth
            size="small"
            label="Old Client ID"
            value={oldClientId}
            onChange={(e) => setOldClientId(e.target.value)}
            sx={fieldSx}
          />

          <TextField
            fullWidth
            size="small"
            label="New Client ID"
            value={newClientId}
            onChange={(e) => setNewClientId(e.target.value)}
            sx={fieldSx}
          />
        </Box>
      )}

      {/* Duplicate controls */}
      {mode === 'duplicate' && (
        <Box
          sx={{
            mt: 2,
            display: 'grid',
            gridTemplateColumns: '1fr',
            gap: 2,
          }}
        >
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
          saving={saving || updating}
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

      {mode === 'delete' && (
        <DeleteModeButtonPanel
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





This comparison appears to be unintentional because the types '"move"' and '"changeAll"' have no overlap.ts(2367)


    <TextField
            fullWidth
            size="small"
            label="Sys/Prin ID"
            placeholder="Enter Sys/Prin ID"
            value={sysPrinInput}
            onChange={(e) => {
              setSysPrinInput(e.target.value);
            }}
            onBlur={() => {
              setSelectedData((prev: any) => ({
                ...(prev ?? {}),
                sysPrin: sysPrinInput,
              }));
            }}
            disabled={!isEditable && (mode === 'changeAll' || mode === 'delete')}
            sx={fieldSx}
          />


