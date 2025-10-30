// parent helper
const onPatchSysPrinsList = (sysPrin, patch, client) => {
  setSysPrinsList(prev => {
    const list = Array.isArray(prev) ? [...prev] : [];
    const idx = list.findIndex(sp => (sp?.id?.sysPrin ?? sp?.sysPrin) === sysPrin
                                   && (client ? (sp?.id?.client ?? sp?.client) === client : true));
    if (idx >= 0) {
      list[idx] = { ...list[idx], ...patch, id: { client, sysPrin, ...(list[idx]?.id ?? {}) } };
    } else {
      list.push({ ...patch, id: { client, sysPrin } });
    }
    return list;
  });
};
