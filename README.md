const onUpdateClick = async () => {
  try {
    const raw = await handleUpdate(viewRow);

    // ✅ Normalize: if server returns text or a "thin" object, use what the user just edited.
    const saved =
      raw && typeof raw === 'object'
        ? { ...viewRow, ...raw } // enrich "thin" payload with the current form values
        : { ...viewRow, _message: typeof raw === 'string' ? raw : undefined };

    showStatus('Client updated successfully', 'success');

    // ✅ Update the modal’s local view immediately
    setSelectedGroupRow(prev => ({ ...(prev ?? {}), ...(saved ?? {}) }));

    // ✅ Bubble to the page so the left list & preview update
    onClientUpdated?.(saved);
  } catch (err) {
    console.error(err);
    showStatus(err.message || 'An error occurred while updating.', 'error');
  }
};
