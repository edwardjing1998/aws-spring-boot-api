const flattenedData = useMemo(() => {
  const rows = FlattenClientData(
    clientList,
    selectedClient,
    expandedGroups,
    isWildcardMode,
    sysPrinPageByClient,
    SYS_PRIN_PAGE_SIZE,
    onClearSelectedData       // ⬅️ new
  );

  // de-dupe logic ...
  ...
}, [clientList, selectedClient, expandedGroups, isWildcardMode, sysPrinPageByClient, onClearSelectedData]);
