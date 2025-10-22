  const buildPayload = () => {
    const client = (selectedGroupRow?.client ?? '').trim();
    const sysPrin = (selectedData?.sysPrin ?? '').trim();
    const data = { ...selectedData, ...statusMap };

    if (!client) throw new Error('Client is required before saving.');
    if (!sysPrin) throw new Error('Sys/Prin is required before saving.');

    return {
      client,
      sysPrin,
      body: {
        client, sysPrin,
        custType: data?.custType ?? '',
        undeliverable: data?.undeliverable ?? '',
        statA: data?.statA ?? '',
        statB: data?.statB ?? '',
        statC: data?.statC ?? '',
        statD: data?.statD ?? '',
        statE: data?.statE ?? '',
        statF: data?.statF ?? '',
        statI: data?.statI ?? '',
        statL: data?.statL ?? '',
        statO: data?.statO ?? '',
        statU: data?.statU ?? '',
        statX: data?.statX ?? '',
        statZ: data?.statZ ?? '',
        poBox: data?.poBox ?? '',
        addrFlag: data?.addrFlag ?? '',
        tempAway: Number(data?.tempAway ?? 0),
        rps: data?.rps ?? '',
        session: data?.session ?? '',
        badState: data?.badState ?? '',
        astatRch: data?.astatRch ?? '',
        nm13: data?.nm13 ?? '',
        tempAwayAtts: Number(data?.tempAwayAtts ?? 0),
        reportMethod: Number(data?.reportMethod ?? 0),
        active: Number(data?.active ?? 0),
        notes: data?.notes ?? '',
        returnStatus: data?.returnStatus ?? '',
        destroyStatus: data?.destroyStatus ?? '',
        nonUS: data?.nonUS ?? '',
        special: data?.special ?? '',
        pinMailer: data?.pinMailer ?? '',
        holdDays: Number(data?.holdDays ?? 0),
        forwardingAddress: data?.forwardingAddress ?? '',
        contact: data?.contact ?? '',
        phone: data?.phone ?? '',
        entityCode: data?.entityCode ?? '',
      }
    };
  };
