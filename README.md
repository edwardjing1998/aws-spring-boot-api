 const onChangeGeneral = (patchOrFn) => {
    setSelectedData(prev => {
      const patch = typeof patchOrFn === 'function' ? patchOrFn(prev) : patchOrFn || {};
      const next  = { ...(prev ?? {}), ...patch };

      // also update the matching row inside sysPrins list so the grid/mapper won't overwrite
      const list = Array.isArray(next.sysPrins) ? [...next.sysPrins] : [];
      const key  = next.sysPrin ?? patch?.sysPrin ?? prev?.sysPrin ?? '';
      const idx  = list.findIndex(sp => (sp?.id?.sysPrin ?? sp?.sysPrin) === key);
      if (idx >= 0) {
        list[idx] = { ...list[idx], ...patch };
        next.sysPrins = list;
      }

      // if you also keep a separate selectedGroupRow, patch it as well
      if (next.selectedGroupRow && (next.selectedGroupRow.sysPrin === key)) {
        next.selectedGroupRow = { ...next.selectedGroupRow, ...patch };
      }

      return next;
    });
  };
