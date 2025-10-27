+ const onPatchSysPrinsList = useCallback((sysPrinCode, patch, clientOpt) => {
+   if (!sysPrinCode) return;
+
+   setClientList((prev) =>
+     prev.map((c) => {
+       const list = Array.isArray(c?.sysPrins) ? c.sysPrins : [];
+       const nextSysPrins = list.map((sp) => {
+         const code   = sp?.id?.sysPrin ?? sp?.sysPrin;
+         const spClient = sp?.id?.client ?? sp?.client ?? c?.client ?? c?.billingSp;
+         const match =
+           code === sysPrinCode &&
+           (clientOpt != null ? spClient === clientOpt : true);
+         return match ? { ...sp, ...patch } : sp;
+       });
+       return list === nextSysPrins ? c : { ...c, sysPrins: nextSysPrins };
+     })
+   );
+
+   // keep the right-hand pane reactive
+   setSelectedData((prev) => (prev ? { ...prev, ...patch } : prev));
+   setSelectedGroupRow((prev) => {
+     if (!prev) return prev;
+     const code   = prev?.id?.sysPrin ?? prev?.sysPrin;
+     const pClient= prev?.id?.client  ?? prev?.client;
+     const match =
+       code === sysPrinCode &&
+       (clientOpt != null ? pClient === clientOpt : true);
+     return match ? { ...prev, ...patch } : prev;
+   });
+ }, []);
