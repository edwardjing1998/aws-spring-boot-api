// ----------------
// DUPLICATE (POST)
// ----------------
const handleDuplicate = async () => {
  const clientId = (selectedGroupRow?.client ?? selectedData?.client ?? '').toString().trim();
  const source   = (selectedData?.sysPrin ?? '').toString().trim();
  const target   = (targetSysPrin ?? '').toString().trim();

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
          msg = j?.message || JSON.stringify(j);
        } else {
          msg = await res.text();
        }
      } catch {
        // ignore parse errors, keep default msg
      }
      throw new Error(msg);
    }

    // ✅ Build the new target record in the SAME style as Change All

    // ① scalar fields from buildCreatePayload (no identity)
    const scalarPatch = getCopyPatchFromSelectedData(); // excludes client/sysPrin/id

    // ② vendor / areas slice
    const vendorPatch = getSourceVendorPatch();

    // ③ final full record for the target
    const fullPatch = {
      ...scalarPatch,
      ...vendorPatch,
      client: clientId,
      sysPrin: target,
      id: { client: clientId, sysPrin: target },
    };

    // --- 1) Update local selectedData so the window reflects the new duplicated row ---
    setSelectedData((prev) => ({
      ...(prev ?? {}),
      ...fullPatch,
    }));

    // --- 2) Update the parent "canonical" list via callback ---
    if (typeof onPatchSysPrinsList === 'function') {
      onPatchSysPrinsList(target, fullPatch, clientId);
    }

    // --- 3) Update selectedGroupRow.sysPrins (preview / right panel) ---
    if (typeof setSelectedGroupRow === 'function') {
      setSelectedGroupRow((prev) => {
        if (!prev) return prev;

        const prevSysPrins = Array.isArray(prev.sysPrins) ? prev.sysPrins : [];

        // If target already exists, merge; otherwise, append
        const exists = prevSysPrins.some((sp) => {
          const code   = sp?.id?.sysPrin ?? sp?.sysPrin;
          const client = sp?.id?.client ?? sp?.client;
          return code === target && client === clientId;
        });

        const nextSysPrins = exists
          ? prevSysPrins.map((sp) => {
              const code   = sp?.id?.sysPrin ?? sp?.sysPrin;
              const client = sp?.id?.client ?? sp?.client;
              if (code === target && client === clientId) {
                return { ...sp, ...fullPatch };
              }
              return sp;
            })
          : [...prevSysPrins, fullPatch];

        return {
          ...prev,
          sysPrins: nextSysPrins,
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
