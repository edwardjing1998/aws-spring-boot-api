const onPatchSysPrinsList = (sysPrin, patch, clientId) => {
  const isRemove = !!patch?.__REMOVE__;

  const makeRow = (existing = {}) => {
    const base = isRemove ? existing : { ...existing, ...patch };
    const idObj = base.id ?? {};
    return {
      ...base,
      client: clientId ?? base.client ?? idObj.client,
      sysPrin: sysPrin ?? base.sysPrin ?? idObj.sysPrin,
      id: { client: clientId ?? idObj.client, sysPrin: sysPrin ?? idObj.sysPrin },
    };
  };

  // 1) Update clientList[*].sysPrins
  setClientList(prev =>
    prev.map(c => {
      if (String(c?.client ?? '') !== String(clientId ?? '')) return c;
      let arr = Array.isArray(c.sysPrins) ? [...c.sysPrins] : [];
      const idx = arr.findIndex(sp => (sp?.id?.sysPrin ?? sp?.sysPrin) === sysPrin);

      if (isRemove) {
        if (idx >= 0) arr.splice(idx, 1);
      } else {
        if (idx >= 0) arr[idx] = makeRow(arr[idx]);
        else arr.push(makeRow());
      }
      return { ...c, sysPrins: arr };
    })
  );

  // 2) Update selectedGroupRow.sysPrins if it belongs to that client
  setSelectedGroupRow(prev => {
    if (!prev || String(prev?.client ?? '') !== String(clientId ?? '')) return prev;
    let arr = Array.isArray(prev.sysPrins) ? [...prev.sysPrins] : [];
    const idx = arr.findIndex(sp => (sp?.id?.sysPrin ?? sp?.sysPrin) === sysPrin);

    if (isRemove) {
      if (idx >= 0) arr.splice(idx, 1);
    } else {
      if (idx >= 0) arr[idx] = makeRow(arr[idx]);
      else arr.push(makeRow());
    }
    return { ...prev, sysPrins: arr };
  });

  // 3) (Optional) if you keep a separate cache…
  setSysPrinsList(prev => {
    const list = Array.isArray(prev) ? [...prev] : [];
    const idx = list.findIndex(
      sp => (sp?.id?.sysPrin ?? sp?.sysPrin) === sysPrin &&
            (clientId ? (sp?.id?.client ?? sp?.client) === clientId : true)
    );
    if (isRemove) {
      if (idx >= 0) list.splice(idx, 1);
    } else {
      if (idx >= 0) list[idx] = makeRow(list[idx]);
      else list.push(makeRow());
    }
    return list;
  });
};
