const buildPayload = useMemo(() => {
  const sd = selectedData ?? {};

  // If your create API truly needs a space for "none", you can map here:
  const normalizeDestroy = (v) => v == null ? ' ' : v; // or keep as-is if your API accepts '0'

  return {
    // identity
    client:   sd.client || '',
    sysPrin:  sd.sysPrin || '',

    // General tab
    custType:       sd.custType ?? '0',
    returnStatus:   sd.returnStatus ?? '',
    destroyStatus:  normalizeDestroy(sd.destroyStatus ?? '0'),
    special:        sd.special ?? '0',
    pinMailer:      sd.pinMailer ?? '0',
    active:         !!sd.active,                 // boolean
    rps:            String(sd.rps ?? '0'),       // '0' | '1'
    addrFlag:       String(sd.addrFlag ?? '0'),  // '0' | '1'
    astatRch:       String(sd.astatRch ?? '0'),  // '0' | '1'
    nm13:           String(sd.nm13 ?? '0'),      // '0' | '1'
    notes:          sd.notes ?? '',

    // ReMail options tab
    undeliverable:      sd.undeliverable ?? '0',
    poBox:              sd.poBox ?? '0',
    tempAway:           Number(sd.tempAway ?? 0) || 0,
    tempAwayAtts:       Number(sd.tempAwayAtts ?? 0) || 0,
    reportMethod:       Number(sd.reportMethod ?? 0) || 0,
    nonUS:              sd.nonUS ?? '0',
    holdDays:           Number(sd.holdDays ?? 0) || 0,
    forwardingAddress:  sd.forwardingAddress ?? '0',
    contact:            sd.contact ?? '',
    phone:              sd.phone ?? '',
    entityCode:         sd.entityCode ?? '0',
    session:            sd.session ?? '',
    badState:           sd.badState ?? '0',
    // (invalidDelivAreas is managed by its own APIs, so usually omitted here)

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
}, [selectedData]);


  // Save handler — POST /create/{client}/{sysPrin}
  const handleSaveCreate = async () => {
    const client = selectedData?.client?.toString().trim();
    const sysPrin = selectedData?.sysPrin?.toString().trim();

    if (!client || !sysPrin) {
      alert('Client and SysPrin are required to create a new record.');
      return;
    }

    const url = `http://localhost:8089/client-sysprin-writer/api/sysprins/create/${encodeURIComponent(client)}/${encodeURIComponent(sysPrin)}`;
    const payload = buildCreatePayload();

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
            msg = j?.message || JSON.stringify(j);
          } else {
            msg = await res.text();
          }
        } catch { /* ignore parse errors */ }
        throw new Error(msg);
      }

      // If API returns created entity, you can merge it into state:
      let created;
      try { created = await res.json(); } catch { /* some APIs return empty body */ }

      // reflect locally so the UI shows the new values immediately
      if (created && typeof created === 'object') {
        setSelectedData(prev => ({ ...(prev ?? {}), ...created }));
        // If parent keeps a canonical list and you want to inject/patch it:
        if (typeof onPatchSysPrinsList === 'function') {
          onPatchSysPrinsList(sysPrin, created, client);
        }
      }

      alert('Sys/Prin created successfully.');
      // you could also close the modal here if desired:
      // onClose?.();
    } catch (e) {
      console.error(e);
      alert(e?.message || 'Failed to create Sys/Prin.');
    } finally {
      setSaving(false);
    }
  };








