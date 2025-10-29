onClientCreated={(saved) => {
  if (!saved) return;
  setClientList((prev) => {
    const match = (c) =>
      String(c?.client ?? '') === String(saved?.client ?? '') ||
      (saved?.billingSp && String(c?.billingSp ?? '') === String(saved?.billingSp ?? ''));
    const idx = prev.findIndex(match);
    if (idx >= 0) {
      const next = [...prev];
      next[idx] = { ...next[idx], ...saved };
      return next;
    }
    return [saved, ...prev];
  });

- setSelectedGroupRow(saved);
+ setSelectedGroupRow((prev) => ({
+   ...(prev ?? {}),
+   ...saved,
+   clientEmail:      saved.clientEmail      ?? prev?.clientEmail,
+   sysPrins:         saved.sysPrins         ?? prev?.sysPrins,
+   sysPrinsPrefixes: saved.sysPrinsPrefixes ?? prev?.sysPrinsPrefixes,
+   reportOptions:    saved.reportOptions    ?? prev?.reportOptions,
+ }));

  setClientInformationWindow({ open: true, mode: 'edit' });
}}
