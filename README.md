// --------------
// DELETE (DELETE)
// --------------
const handleDeleteSysPrin = async () => {
  const clientId = (selectedGroupRow?.client ?? selectedData?.client ?? '').toString().trim();
  const sysPrinCode = (selectedData?.sysPrin ?? '').toString().trim();

  if (!clientId || !sysPrinCode) {
    alert('Client and SysPrin are required to delete.');
    return;
  }

  // Optional: confirm
  if (!window.confirm(`Are you sure you want to delete Sys/Prin "${sysPrinCode}" for client "${clientId}"?`)) {
    return;
  }

  const url =
    `http://localhost:8089/client-sysprin-writer/api/sysprins/${encodeURIComponent(clientId)}` +
    `/${encodeURIComponent(sysPrinCode)}`;

  setSaving(true);
  try {
    const res = await fetch(url, { method: 'DELETE', headers: { accept: '*/*' } });
    if (!res.ok) {
      let msg = `Delete Sys/Prin failed (${res.status})`;
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

    // --- 1) Tell parent to remove this sysPrin from its canonical list ---
    if (typeof onPatchSysPrinsList === 'function') {
      // parent should interpret { __REMOVE__: true } as "delete this sysPrin"
      onPatchSysPrinsList(sysPrinCode, { __REMOVE__: true }, clientId);
    }

    // --- 2) Remove from selectedGroupRow.sysPrins (preview card) ---
    if (typeof setSelectedGroupRow === 'function') {
      setSelectedGroupRow((prev) => {
        if (!prev) return prev;
        const prevSysPrins = Array.isArray(prev.sysPrins) ? prev.sysPrins : [];
        const nextSysPrins = prevSysPrins.filter((sp) => {
          const code = sp?.id?.sysPrin ?? sp?.sysPrin;
          const client = sp?.id?.client ?? sp?.client;
          return !(code === sysPrinCode && client === clientId);
        });
        return { ...prev, sysPrins: nextSysPrins };
      });
    }

    // --- 3) (Optional) Clear local selectedData if it points to this deleted row ---
    setSelectedData((prev) => {
      if (!prev) return prev;
      const code = prev?.id?.sysPrin ?? prev?.sysPrin;
      const client = prev?.id?.client ?? prev?.client;
      if (code === sysPrinCode && client === clientId) {
        // You can choose null or {} depending on how parent uses it
        return null;
      }
      return prev;
    });

    alert('Delete Sys/Prin completed successfully.');
    // Optionally close the window:
    // onClose && onClose();
  } catch (e) {
    console.error(e);
    alert(e?.message || 'Failed to delete Sys/Prin.');
  } finally {
    setSaving(false);
  }
};
