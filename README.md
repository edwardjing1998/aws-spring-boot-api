onClientUpdated={(saved) => {
  if (!saved) return;

  // 1) upsert list item (as you already do)
  setClientList((prev) => {
    const match = (c) =>
      String(c?.client ?? '') === String(saved?.client ?? '') ||
      (saved?.billingSp && String(c?.billingSp ?? '') === String(saved?.billingSp ?? ''));
    const idx = prev.findIndex(match);
    if (idx === -1) return prev;
    const next = [...prev];
    next[idx] = { ...next[idx], ...saved };   // list can be shallow-merged
    return next;
  });

  // 2) preserve heavy slices on the selected row while overlaying 'saved'
- setSelectedGroupRow(saved);
+ setSelectedGroupRow((prev) => {
+   if (!prev) return saved;
+   return {
+     ...prev,
+     ...saved,
+     // preserve nested collections not returned by update API
+     clientEmail:       saved.clientEmail       ?? prev.clientEmail,
+     sysPrins:          saved.sysPrins          ?? prev.sysPrins,
+     sysPrinsPrefixes:  saved.sysPrinsPrefixes  ?? prev.sysPrinsPrefixes,
+     reportOptions:     saved.reportOptions     ?? prev.reportOptions,
+   };
+ });
}}
