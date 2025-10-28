  const handleUpdate = async () => {
    ...
      let saved = null;
      try { saved = await res.json(); } catch {}
-     // const saved = await res.json(); // if the API returns the updated sysPrin
-     alert('Re-mail options updated successfully.');
-     // Optional: merge returned server copy back into selectedData
-     // setSelectedData?.(prev => ({ ...prev, ...(saved ?? {}) }));
+     // Construct the canonical patch we know we updated on this page:
+     const patch = saved ?? {
+       holdDays: buildPayload.holdDays,
+       tempAway: buildPayload.tempAway,
+       tempAwayAtts: buildPayload.tempAwayAtts,
+       undeliverable: buildPayload.undeliverable,
+       forwardingAddress: buildPayload.forwardingAddress,
+       nonUS: buildPayload.nonUS,
+       poBox: buildPayload.poBox,
+       badState: buildPayload.badState,
+       // invalidDelivAreas handled by its own APIs; we already bubble changes when add/delete
+     };
+     // bubble to parent so clientList/sysPrins stays in sync
+     pushGeneralPatch(patch);
+     alert('Re-mail options updated successfully.');
