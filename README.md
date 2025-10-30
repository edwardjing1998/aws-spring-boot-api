  const handleUpdate = async () => {
    if (!selectedGroupRow?.client) {
      alert('Client ID is required.');
      return;
    }
    // ...build payload (unchanged)

    try {
      setUpdating(true);
      const response = await fetch(
        'http://localhost:8089/client-sysprin-writer/api/client/update',
        {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify(payload),
        }
      );

      if (!response.ok) {
        const text = await response.text();
        throw new Error(`Save failed: ${response.status} ${text}`);
      }
-     const saved = await response.json();
-     console.log('Client updated successfully:', saved);
+     // ✅ Normalize response (server might return JSON or plain text, and may be "thin")
+     const ct = response.headers.get('content-type') || '';
+     const savedRaw = ct.includes('application/json') ? await response.json() : await response.text();
+     const saved = (savedRaw && typeof savedRaw === 'object')
+       ? savedRaw
+       : { ...payload, _message: typeof savedRaw === 'string' ? savedRaw : undefined };
+
+     // ✅ keep local state in sync for the modal
+     setSelectedGroupRow(prev => ({ ...(prev ?? {}), ...(saved ?? {}) }));
+
+     // ✅ bubble to parent so list + preview update
+     onClientUpdated?.(saved);
+
+     console.log('Client updated successfully:', saved);
    } catch (err) {
      console.error(err);
      alert('An error occurred while saving. Check console for details.');
    } finally {
      setUpdating(false);
    }
  };
