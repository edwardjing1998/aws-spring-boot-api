const pushGeneralPatch = (patch) => {
  const withKeys = {
    client: selectedData?.client,
    sysPrin: selectedData?.sysPrin,
    ...(patch || {}),
  };

  // Prefer the shared updater so it also syncs preview + parent list
  if (typeof onChangeGeneral === 'function') {
    onChangeGeneral(withKeys);
  } else {
    // Fallback: at least keep selectedData in sync
    setSelectedData((prev) => ({
      ...(prev ?? {}),
      ...withKeys,
    }));
  }
};
