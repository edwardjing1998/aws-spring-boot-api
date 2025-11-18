// ----------------
// CHANGE ALL (POST)
// ----------------
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

    // ① Base patch from all the scalar fields
    const scalarPatch = getCopyPatchFromSelectedData();

    // ② Vendor/areas/notes from the current (source) sysPrin
    const vendorPatch = getSourceVendorPatch();

    // ③ Final patch that we want to apply to *every other* sysPrin
    const fullPatch = { ...scalarPatch, ...vendorPatch };

    // 1) Update parent grid/canonical list for every target sysPrin under this client
    if (typeof onPatchSysPrinsList === 'function') {
      const sysPrins = Array.isArray(selectedGroupRow?.sysPrins) ? selectedGroupRow.sysPrins : [];
      sysPrins
        .map((sp) => sp?.id?.sysPrin ?? sp?.sysPrin)
        .filter((sp) => sp && sp !== sourceSysPrin)
        .forEach((target) => {
          onPatchSysPrinsList(
            target,
            { ...fullPatch, id: { client: clientId, sysPrin: target } },
            clientId
          );
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
            // apply the *same* patch here
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
  } catch (e) {
    console.error(e);
    alert(e?.message || 'Failed to Change All.');
  } finally {
    setSaving(false);
  }
};
