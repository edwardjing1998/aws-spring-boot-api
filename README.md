   // Save handler — POST /create/{client}/{sysPrin}
   const handleSaveCreate = async () => {
     const client = selectedGroupRow?.client?.toString().trim();
     const sysPrin = selectedData?.sysPrin?.toString().trim();

     if (!client || !sysPrin) {
       alert('Client and SysPrin are required to create a new record.');
       return;
     }

     const url = `http://localhost:8089/client-sysprin-writer/api/sysprins/create/${encodeURIComponent(client)}/${encodeURIComponent(sysPrin)}`;
-    const payload = buildCreatePayload;
+    const payload = buildCreatePayload; // useMemo already returns the object

     setSaving(true);
     try {
       const res = await fetch(url, {
         method: 'POST',
         headers: { accept: '*/*', 'Content-Type': 'application/json' },
         body: JSON.stringify(payload),
       });

       if (!res.ok) {
         let msg = `Create failed (${res.status})`;
         try {
           const ct = res.headers.get('Content-Type') || '';
           if (ct.includes('application/json')) {
             const j = await res.json();
             msg = j?.message || JSON.stringify(j);
           } else {
             msg = await res.text();
           }
         } catch { /* ignore parse errors */ }
         throw new Error(msg);
       }

       // If API returns created entity, you can merge it into state:
       let created;
       try { created = await res.json(); } catch { /* some APIs return empty body */ }

+      // --- Normalize the data we’ll propagate upward
+      const canonical = created && typeof created === 'object' ? created : payload;
+      // ensure identity fields are set
+      canonical.client = client;
+      canonical.sysPrin = sysPrin;
+      // some lists use an id object
+      const withId = {
+        ...canonical,
+        id: canonical.id ?? { client, sysPrin },
+      };

       // reflect locally so the UI shows the new values immediately
-      if (created && typeof created === 'object') {
-        setSelectedData(prev => ({ ...(prev ?? {}), ...created }));
-        // If parent keeps a canonical list and you want to inject/patch it:
-        if (typeof onPatchSysPrinsList === 'function') {
-          onPatchSysPrinsList(sysPrin, created, client);
-        }
-      }
+      setSelectedData(prev => ({ ...(prev ?? {}), ...withId }));
+
+      // keep grid/canonical list in parent synced (owner of sysPrinsList)
+      if (typeof onPatchSysPrinsList === 'function') {
+        onPatchSysPrinsList(sysPrin, withId, client);
+      }
+
+      // also update parent’s selectedGroupRow (if parent provided a setter)
+      if (typeof setSelectedGroupRow === 'function') {
+        setSelectedGroupRow(prev => {
+          const prevId = prev?.id ?? {};
+          return {
+            ...(prev ?? {}),
+            ...withId,
+            id: { client, sysPrin, ...prevId },
+          };
+        });
+      }

       alert('Sys/Prin created successfully.');
       // you could also close the modal here if desired:
       // onClose?.();
     } catch (e) {
       console.error(e);
       alert(e?.message || 'Failed to create Sys/Prin.');
     } finally {
       setSaving(false);
     }
   };
