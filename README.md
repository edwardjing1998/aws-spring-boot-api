const handleClientUpdated = useCallback((saved) => {
  if (!saved) return;
- setClientList((prev) => upsertClient(prev, saved));
- setSelectedGroupRow((prev) => ({ ...(prev ?? {}), ...(saved ?? {}) }));
+ setClientList((prev) => {
+   const match = (c) =>
+     String(c?.client ?? '') === String(saved?.client ?? '') ||
+     (saved?.billingSp && String(c?.billingSp ?? '') === String(saved?.billingSp ?? ''));
+   const idx = prev.findIndex(match);
+   if (idx === -1) return prev;
+   const next = [...prev];
+   next[idx] = { ...next[idx], ...saved };
+   return next;
+ });
+ setSelectedGroupRow(saved);
}, []);
