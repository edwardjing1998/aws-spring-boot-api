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
