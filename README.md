<ClientInformationWindow
  ...
- onClientCreated={(saved) => {
-   if (!saved) return;
-   setClientList((prev) => {
-     const idx = prev.findIndex((c) => c.client === saved.client);
-     if (idx >= 0) {
-       const copy = [...prev];
-       copy[idx] = { ...copy[idx], ...saved };
-       return copy;
-     }
-     return [saved, ...prev];
-   });
-   setSelectedGroupRow(saved);
-   setClientInformationWindow({ open: true, mode: 'edit' });
- }}
+ onClientCreated={(saved) => {
+   if (!saved) return;
+   setClientList((prev) => {
+     const match = (c) =>
+       String(c?.client ?? '') === String(saved?.client ?? '') ||
+       (saved?.billingSp && String(c?.billingSp ?? '') === String(saved?.billingSp ?? ''));
+     const idx = prev.findIndex(match);
+     if (idx >= 0) {
+       const next = [...prev];
+       next[idx] = { ...next[idx], ...saved };
+       return next;
+     }
+     return [saved, ...prev];
+   });
+   // Replace, don't merge, so preview shows the actual saved client payload
+   setSelectedGroupRow(saved);
+   setClientInformationWindow({ open: true, mode: 'edit' });
+ }}
  ...
- onClientUpdated={(saved) => {
-   if (!saved) return;
-   setClientList((prev) =>
-     prev.map((c) => (c.client === saved.client ? { ...c, ...saved } : c))
-   );
-   setSelectedGroupRow((prev) => ({ ...(prev ?? {}), ...(saved ?? {}) }));
- }}
+ onClientUpdated={(saved) => {
+   if (!saved) return;
+   setClientList((prev) => {
+     const match = (c) =>
+       String(c?.client ?? '') === String(saved?.client ?? '') ||
+       (saved?.billingSp && String(c?.billingSp ?? '') === String(saved?.billingSp ?? ''));
+     const idx = prev.findIndex(match);
+     if (idx === -1) return prev; // nothing to do
+     const next = [...prev];
+     next[idx] = { ...next[idx], ...saved };
+     return next;
+   });
+   // Replace instead of merge to avoid keeping an old "group" shape
+   setSelectedGroupRow(saved);
+ }}
/>
