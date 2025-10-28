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
      body: JSON.stringify(buildPayload),
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
      holdDays: buildPayload.holdDays,
      tempAway: buildPayload.tempAway,
      tempAwayAtts: buildPayload.tempAwayAtts,
      undeliverable: buildPayload.undeliverable,
      forwardingAddress: buildPayload.forwardingAddress,
      nonUS: buildPayload.nonUS,
      poBox: buildPayload.poBox,
      badState: buildPayload.badState,
      // invalidDelivAreas handled by its own create/delete APIs
    };

    // Bubble up so parent list stays in sync
    pushGeneralPatch(patch);

    alert('Re-mail options updated successfully.');
  } catch (e) {
    console.error(e);
    alert(e?.message || 'Failed to update.');
  } finally {
    setUpdating(false);
  }
};
