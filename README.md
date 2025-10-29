const handleClientUpdated = useCallback((saved) => {
  if (!saved) return;
  setClientList((prev) => {
    const match = (c) =>
      String(c?.client ?? '') === String(saved?.client ?? '') ||
      (saved?.billingSp && String(c?.billingSp ?? '') === String(saved?.billingSp ?? ''));
    const idx = prev.findIndex(match);
-   if (idx === -1) return prev;
+   // If not found (e.g., client id changed), insert saved instead of no-op
+   if (idx === -1) return [saved, ...prev];
    const next = [...prev];
    next[idx] = { ...next[idx], ...saved };
    return next;
  });
- setSelectedGroupRow(saved);
+ // Preserve heavy/nested slices already loaded in the preview
+ setSelectedGroupRow((prev) => {
+   if (!prev) return saved;
+   return {
+     ...prev,
+     ...saved,
+     clientEmail:       saved.clientEmail       ?? prev.clientEmail,
+     sysPrins:          saved.sysPrins          ?? prev.sysPrins,
+     sysPrinsPrefixes:  saved.sysPrinsPrefixes  ?? prev.sysPrinsPrefixes,
+     reportOptions:     saved.reportOptions     ?? prev.reportOptions,
+   };
+ });
}, []);
