// ----------------
// DUPLICATE (POST)
// ----------------
const handleDuplicate = async () => {
  const client =
    (selectedGroupRow?.client ?? selectedData?.client ?? '')
      .toString()
      .trim();
  const sourceSysPrin =
    (selectedData?.sysPrin ?? '')
      .toString()
      .trim();
  const target =
    (targetSysPrin ?? '')
      .toString()
      .trim();

  if (!client || !sourceSysPrin || !target) {
    alert('Client, Source SysPrin, and Target SysPrin are required.');
    return;
  }
  if (sourceSysPrin === target) {
    alert('Target SysPrin must be different from Source SysPrin.');
    return;
  }

  const url =
    `http://localhost:8089/client-sysprin-writer/api/clients/${encodeURIComponent(client)}` +
    `/sysprins/${encodeURIComponent(sourceSysPrin)}` +
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
        // ignore parse error; keep default message
      }
      throw new Error(msg);
    }

    // Try to read JSON; tolerate empty body
    let dup = null;
    try {
      const ct = res.headers.get('Content-Type') || '';
      if (ct.includes('application/json')) {
        dup = await res.json();
      }
    } catch {
      dup = null;
    }

    // Canonical duplicated record (same style as handleSaveCreate)
    const base =
      dup && typeof dup === 'object'
        ? dup
        : { ...(selectedData ?? {}) };

    const canonical = {
      ...base,
      client,
      sysPrin: target,
    };

    const withId = {
      ...canonical,
      id: canonical.id ?? { client, sysPrin: target },
    };

    // ① Update local window state
    setSelectedData((prev) => ({
      ...(prev ?? {}),
      ...withId,
    }));

    // ② Update parent "real" list — same call pattern as handleSaveCreate
    if (typeof onPatchSysPrinsList === 'function') {
      console.log('[duplicate] onPatchSysPrinsList args:', target, withId, client);
      onPatchSysPrinsList(target, withId, client);
    }

    // ③ Update selectedGroupRow / preview card
    if (typeof setSelectedGroupRow === 'function') {
      setSelectedGroupRow((prev) => {
        if (!prev) return prev;

        const prevId = prev.id ?? {};
        const prevClient = prev.client ?? prevId.client;
        const prevSysPrins = Array.isArray(prev.sysPrins) ? prev.sysPrins : [];

        // Only touch this preview if it is for this client
        if (prevClient !== client) {
          return prev;
        }

        const exists = prevSysPrins.some((sp) => {
          const code = sp?.id?.sysPrin ?? sp?.sysPrin;
          return code === target;
        });

        const nextSysPrins = exists
          ? prevSysPrins.map((sp) => {
              const code = sp?.id?.sysPrin ?? sp?.sysPrin;
              return code === target ? { ...sp, ...withId } : sp;
            })
          : [...prevSysPrins, withId];

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
