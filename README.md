// utils/ClientInformationWindow.jsx (inside the component)
const onUpdateClick = async () => {
  try {
    const savedRaw = await handleUpdate(viewRow);

    // Normalize: if server returned a string or an object without client id,
    // fall back to current viewRow (which *does* have client).
    const saved =
      savedRaw && typeof savedRaw === 'object' && savedRaw.client
        ? savedRaw
        : { ...viewRow, ...(typeof savedRaw === 'object' ? savedRaw : {}) };

    showStatus('Client updated successfully', 'success');

    // keep local modal state in-sync
    setSelectedGroupRow(prev => ({ ...(prev ?? {}), ...(saved ?? {}) }));

    // bubble to page (updates list + preview panel)
    onClientUpdated?.(saved);
  } catch (err) {
    console.error(err);
    showStatus(err.message || 'An error occurred while updating.', 'error');
  }
};
