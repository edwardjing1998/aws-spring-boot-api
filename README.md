// ------------
 // MOVE (PUT)
 // ------------
 const handleMove = async () => {
   // Prefer the live input if present, then fall back to selectedData
   const sysPrinCode =
     (sysPrinInput || selectedData?.sysPrin || '').toString().trim();

   const oldId =
     (oldClientId || selectedData?.client || selectedGroupRow?.client || '')
       .toString()
       .trim();

   const newId = (newClientId || '').toString().trim();

   if (!sysPrinCode || !oldId || !newId) {
     alert('Old Client ID, New Client ID, and SysPrin are required.');
     return;
   }

   const url =
     `http://localhost:8089/client-sysprin-writer/api/sysprins/move-sysprin` +
     `?oldClientId=${encodeURIComponent(oldId)}` +
     `&sysPrin=${encodeURIComponent(sysPrinCode)}` +
     `&newClientId=${encodeURIComponent(newId)}`;

   setSaving(true);
   try {
     const res = await fetch(url, { method: 'PUT', headers: { accept: '*/*' } });
     if (!res.ok) {
       let msg = `Move failed (${res.status})`;
       try {
         const ct = res.headers.get('Content-Type') || '';
         if (ct.includes('application/json')) {
           const j = await res.json();
           msg = j?.message || JSON.stringify(j);
         } else {
           msg = await res.text();
         }
       } catch {}
       throw new Error(msg);
     }

     // --- 1) Build the "moved" record locally ---
     const moved = {
       ...(selectedData ?? {}),
       client: newId,
       sysPrin: sysPrinCode,
       id: { client: newId, sysPrin: sysPrinCode },
     };

     // update local selectedData so the window reflects the new client
     setSelectedData((prev) => ({ ...(prev ?? {}), ...moved }));

     // --- 2) Tell parent list to remove from old client and add to new client ---
     if (typeof onPatchSysPrinsList === 'function') {
       // parent should interpret { __REMOVE__: true } as "delete this sysPrin" from old client
       onPatchSysPrinsList(sysPrinCode, { __REMOVE__: true }, oldId);
       // and then add/update under the new client
       onPatchSysPrinsList(sysPrinCode, moved, newId);
     }

     // --- 3) Update selectedGroupRow.sysPrins (preview card) ---
     // remove this sysPrin from the old client group
     if (typeof setSelectedGroupRow === 'function') {
       setSelectedGroupRow((prev) => {
         if (!prev) return prev;
         const prevSysPrins = Array.isArray(prev.sysPrins) ? prev.sysPrins : [];

         const nextSysPrins = prevSysPrins.filter((sp) => {
           const code   = sp?.id?.sysPrin ?? sp?.sysPrin;
           const client = sp?.id?.client ?? sp?.client;
           return !(code === sysPrinCode && client === oldId);
         });

         return { ...prev, sysPrins: nextSysPrins };
       });
     }

     alert('Sys/Prin moved successfully.');
   } catch (e) {
     console.error(e);
     alert(e?.message || 'Failed to move Sys/Prin.');
   } finally {
     setSaving(false);
   }
 };
