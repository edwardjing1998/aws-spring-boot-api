const handleChangeAll = async () => {
  const source = selectedData?.sysPrin?.toString().trim();
  const clientId =
    (oldClientId?.toString().trim()) ||
    (selectedGroupRow?.client?.toString().trim()) ||
    '';

  if (!clientId || !source) {
    alert('Client and Source SysPrin are required for Change All.');
    return;
  }

  const url =
    `http://localhost:8089/client-sysprin-writer/api/clients/${encodeURIComponent(clientId)}` +
    `/sysprins/copy-from/${encodeURIComponent(source)}`;

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

    // If server returns the updated list, you can parse and merge here.
    // Many backends return empty; we’ll optimistic-update parent:
    const patch = getCopyPatchFromSelectedData();

    // Find all targets in this client except the source
    const targets = Array.isArray(selectedGroupRow?.sysPrins)
      ? selectedGroupRow.sysPrins
          .map(sp => sp?.sysPrin ?? sp?.id?.sysPrin)
          .filter(code => code && code !== source)
      : [];

    // Apply patch to each target via parent's canonical updater
    if (typeof onPatchSysPrinsList === 'function') {
      targets.forEach(targetCode => {
        onPatchSysPrinsList(targetCode, {
          ...patch,
          // keep identity consistent in each patched entry
          client: clientId,
          sysPrin: targetCode,
          id: { client: clientId, sysPrin: targetCode }
        }, clientId);
      });
    }

    // Also refresh our local selectedGroupRow (cheap mirror, but parent patch above already does it)
    if (typeof setSelectedGroupRow === 'function') {
      setSelectedGroupRow(prev => {
        if (!prev || String(prev?.client ?? '') !== String(clientId)) return prev;
        const nextSysPrins = Array.isArray(prev.sysPrins) ? prev.sysPrins.map(sp => {
          const code = sp?.sysPrin ?? sp?.id?.sysPrin;
          if (!code || code === source) return sp; // skip source
          return { ...sp, ...patch, sysPrin: code, client: clientId, id: { client: clientId, sysPrin: code } };
        }) : prev.sysPrins;
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
