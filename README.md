-  const onPatchSysPrinsList = (sysPrin, patch, client) => {
-  setSysPrinsList(prev => {
-    const list = Array.isArray(prev) ? [...prev] : [];
-    const idx = list.findIndex(sp => (sp?.id?.sysPrin ?? sp?.sysPrin) === sysPrin
-                                   && (client ? (sp?.id?.client ?? sp?.client) === client : true));
-    if (idx >= 0) {
-      list[idx] = { ...list[idx], ...patch, id: { client, sysPrin, ...(list[idx]?.id ?? {}) } };
-    } else {
-      list.push({ ...patch, id: { client, sysPrin } });
-    }
-    return list;
-  });
-};
+  const onPatchSysPrinsList = (sysPrin, patch, clientId) => {
+    const makeRow = (existing = {}) => {
+      const base = { ...existing, ...patch };
+      const idObj = base.id ?? {};
+      return {
+        ...base,
+        client: clientId ?? base.client,
+        sysPrin: sysPrin ?? base.sysPrin ?? idObj.sysPrin,
+        id: { client: clientId ?? idObj.client, sysPrin: sysPrin ?? idObj.sysPrin },
+      };
+    };
+
+    // 1) Update the source-of-truth list used by NavigationPanel/preview: clientList[*].sysPrins
+    setClientList(prev =>
+      prev.map(c => {
+        if (String(c?.client ?? '') !== String(clientId ?? '')) return c;
+        const arr = Array.isArray(c.sysPrins) ? [...c.sysPrins] : [];
+        const idx = arr.findIndex(sp => (sp?.id?.sysPrin ?? sp?.sysPrin) === sysPrin);
+        if (idx >= 0) {
+          arr[idx] = makeRow(arr[idx]);
+        } else {
+          arr.push(makeRow());
+        }
+        return { ...c, sysPrins: arr };
+      })
+    );
+
+    // 2) Update the right-pane header card object too: selectedGroupRow.sysPrins
+    setSelectedGroupRow(prev => {
+      if (!prev || String(prev?.client ?? '') !== String(clientId ?? '')) return prev;
+      const arr = Array.isArray(prev.sysPrins) ? [...prev.sysPrins] : [];
+      const idx = arr.findIndex(sp => (sp?.id?.sysPrin ?? sp?.sysPrin) === sysPrin);
+      if (idx >= 0) {
+        arr[idx] = makeRow(arr[idx]);
+      } else {
+        arr.push(makeRow());
+      }
+      return { ...prev, sysPrins: arr };
+    });
+
+    // 3) (Optional) keep your local cache in sync too
+    setSysPrinsList(prev => {
+      const list = Array.isArray(prev) ? [...prev] : [];
+      const idx = list.findIndex(
+        sp => (sp?.id?.sysPrin ?? sp?.sysPrin) === sysPrin &&
+              (clientId ? (sp?.id?.client ?? sp?.client) === clientId : true)
+      );
+      if (idx >= 0) {
+        list[idx] = makeRow(list[idx]);
+      } else {
+        list.push(makeRow());
+      }
+      return list;
+    });
+  };
