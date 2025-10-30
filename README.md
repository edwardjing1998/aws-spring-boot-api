 // Save handler — POST /create/{client}/{sysPrin}
 ...
   };
 
+  // Move handler — PUT /move-sysprin?oldClientId=...&sysPrin=...&newClientId=...
+  const handleMove = async () => {
+    const sysPrin = selectedData?.sysPrin?.toString().trim();
+    const oldId = oldClientId?.toString().trim();
+    const newId = newClientId?.toString().trim();
+
+    if (!sysPrin || !oldId || !newId) {
+      alert('Old Client ID, New Client ID, and SysPrin are required.');
+      return;
+    }
+
+    const url =
+      `http://localhost:8089/client-sysprin-writer/api/sysprins/move-sysprin` +
+      `?oldClientId=${encodeURIComponent(oldId)}` +
+      `&sysPrin=${encodeURIComponent(sysPrin)}` +
+      `&newClientId=${encodeURIComponent(newId)}`;
+
+    setSaving(true);
+    try {
+      const res = await fetch(url, { method: 'PUT', headers: { accept: '*/*' } });
+      if (!res.ok) {
+        let msg = `Move failed (${res.status})`;
+        try {
+          const ct = res.headers.get('Content-Type') || '';
+          if (ct.includes('application/json')) {
+            const j = await res.json();
+            msg = j?.message || JSON.stringify(j);
+          } else {
+            msg = await res.text();
+          }
+        } catch {}
+        throw new Error(msg);
+      }
+
+      // Server may return nothing; build a canonical row locally
+      const moved = {
+        ...(selectedData ?? {}),
+        client: newId,
+        sysPrin,
+        id: { client: newId, sysPrin },
+      };
+
+      // Update local detail states
+      setSelectedData((prev) => ({ ...(prev ?? {}), ...moved }));
+      if (typeof setSelectedGroupRow === 'function') {
+        setSelectedGroupRow((prev) => {
+          // keep other slices; only adjust sysPrins list if prev is old client
+          if (!prev) return prev;
+          return { ...prev }; // parent will do list maintenance via callbacks below
+        });
+      }
+
+      // Tell parent to remove from old client and add to new client.
+      // We reuse onPatchSysPrinsList with a small convention:
+      //   pass { __REMOVE__: true } to indicate removal.
+      if (typeof onPatchSysPrinsList === 'function') {
+        // remove from old
+        onPatchSysPrinsList(sysPrin, { __REMOVE__: true }, oldId);
+        // add/patch into new
+        onPatchSysPrinsList(sysPrin, moved, newId);
+      }
+
+      alert('Sys/Prin moved successfully.');
+      // Optionally switch the modal to "edit" mode after move:
+      // onClose?.();
+    } catch (e) {
+      console.error(e);
+      alert(e?.message || 'Failed to move Sys/Prin.');
+    } finally {
+      setSaving(false);
+    }
+  };
