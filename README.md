const handleDuplicate = async () => {
  const clientId  = (selectedGroupRow?.client ?? selectedData?.client ?? '')
    .toString()
    .trim();
  const source    = (selectedData?.sysPrin ?? '').toString().trim();
  const target    = (targetSysPrin ?? '').toString().trim();

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
      } catch (err) {
        // ignore parse errors; keep default msg
      }

      throw new Error(msg);
    }

    // ① Try to read JSON; tolerate empty body
    let dup = null;
    try {
      dup = await res.json();
    } catch {
      dup = null;
    }

    const base =
      dup && typeof dup === 'object'
        ? dup
        : { ...(selectedData ?? {}) };

    const canonical = {
      ...base,
      client: clientId,
      sysPrin: target,
    };

    const withId = {
      ...canonical,
      id: canonical.id ?? { client: clientId, sysPrin: target },
    };

    // ② Update *this window*'s local selectedData
    setSelectedData((prev) => ({
      ...(prev ?? {}),
      ...withId,
    }));

    // ③ Use the shared pipeline so parent list state (sysPrinsList) is updated
    //    onChangeGeneral internally calls onPatchSysPrinsList if provided.
    if (typeof onChangeGeneral === 'function') {
      onChangeGeneral({
        ...withId,
        client: clientId,
        sysPrin: target,
      });
    } else if (typeof onPatchSysPrinsList === 'function') {
      // Fallback: talk directly to parent
      onPatchSysPrinsList(
        target,
        {
          ...withId,
          client: clientId,
          sysPrin: target,
          id: { client: clientId, sysPrin: target },
        },
        clientId,
      );
    }

    // ④ Explicitly refresh the preview card / selectedGroupRow.sysPrins
    if (typeof setSelectedGroupRow === 'function') {
      setSelectedGroupRow((prev) => {
        if (!prev) return prev;

        const prevSysPrins = Array.isArray(prev.sysPrins) ? prev.sysPrins : [];

        const exists = prevSysPrins.some((sp) => {
          const spCode = sp?.id?.sysPrin ?? sp?.sysPrin;
          const spClient = sp?.id?.client ?? sp?.client;
          return spCode === target && spClient === clientId;
        });

        const nextSysPrins = exists
          ? prevSysPrins.map((sp) => {
              const spCode = sp?.id?.sysPrin ?? sp?.sysPrin;
              const spClient = sp?.id?.client ?? sp?.client;
              return spCode === target && spClient === clientId
                ? { ...sp, ...withId }
                : sp;
            })
          : [...prevSysPrins, withId];

        return {
          ...prev,
          client: clientId,
          id: { ...(prev.id ?? {}), client: clientId },
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
