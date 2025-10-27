// Focused updater for "general" fields (contact/phone/flags/dropdowns/etc.)
const onChangeGeneral = (patchOrFn) => {
  setSelectedData((prev) => {
    // 1) Normalize the incoming patch (allow function or plain object)
    const rawPatch = typeof patchOrFn === 'function' ? patchOrFn(prev) : (patchOrFn || {});
    // Don't let "undefined" blow away existing values
    const patch = Object.fromEntries(Object.entries(rawPatch).filter(([, v]) => v !== undefined));

    // 2) Merge into top-level selectedData
    const next = { ...(prev ?? {}), ...patch };

    // 3) Work out the identity keys for the currently-open row
    const keySysPrin = patch.sysPrin ?? next.sysPrin ?? prev?.sysPrin ?? '';
    const keyClient  = patch.client  ?? next.client  ?? prev?.client  ?? undefined;

    // 4) Also update the matching row inside sysPrins list so re-mapping won't overwrite
    const listFromPrev = Array.isArray(prev?.sysPrins) ? prev.sysPrins : [];
    const listFromNext = Array.isArray(next?.sysPrins) ? next.sysPrins : [];
    const list = [...(listFromPrev.length ? listFromPrev : listFromNext)];

    const matchFn = (sp) => {
      const spCode   = sp?.id?.sysPrin ?? sp?.sysPrin;
      const spClient = sp?.id?.client  ?? sp?.client;
      if (!spCode) return false;
      if (keyClient != null) {
        // If we know client, match both sysPrin & client (safer for multi-client screens)
        return spCode === keySysPrin && spClient === keyClient;
      }
      // Otherwise match by sysPrin only
      return spCode === keySysPrin;
    };

    const idx = list.findIndex(matchFn);
    if (idx >= 0) {
      // Shallow-merge patch into the grid row
      list[idx] = { ...list[idx], ...patch };
      next.sysPrins = list;
    } else if (list.length) {
      // No exact match found; still assign back the list we had to avoid dropping it
      next.sysPrins = list;
    }

    // 5) If you keep a separate selectedGroupRow, patch it as well
    if (next.selectedGroupRow) {
      const sg = next.selectedGroupRow;
      const sgCode   = sg?.id?.sysPrin ?? sg?.sysPrin;
      const sgClient = sg?.id?.client  ?? sg?.client;
      const groupMatches =
        sgCode === keySysPrin && (keyClient != null ? sgClient === keyClient : true);

      if (groupMatches) {
        next.selectedGroupRow = { ...sg, ...patch };
      }
    }

    // 6) Return the new state
    return next;
  });
};
