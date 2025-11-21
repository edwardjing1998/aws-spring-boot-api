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
        // ignore parse errors, keep default msg
      }

      throw new Error(msg);
    }

    // try to read JSON, but tolerate empty body
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

    // ✅ 1) Keep local selectedData in sync for this window
    setSelectedData((prev) => ({
      ...(prev ?? {}),
      ...withId,
    }));

    // ✅ 2) Use the SAME pipeline as edit/create:
    // onChangeGeneral already:
    //  - updates selectedData
    //  - updates selectedGroupRow.sysPrins
    //  - calls onPatchSysPrinsList for the parent
    if (typeof onChangeGeneral === 'function') {
      onChangeGeneral({
        ...withId,
        client: clientId,
        sysPrin: target,
      });
    }

    // ❌ 3) We no longer need to manually call onPatchSysPrinsList
    //     or setSelectedGroupRow here. onChangeGeneral handles that.

    alert('Sys/Prin duplicated successfully.');
  } catch (e) {
    console.error(e);
    alert(e?.message || 'Failed to duplicate Sys/Prin.');
  } finally {
    setSaving(false);
  }
};
