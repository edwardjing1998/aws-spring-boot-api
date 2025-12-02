const handleRowClicked = (event: RowClickedEvent<NavigationRow>) => {
  const row = event.data;
  if (!row) return;
  const clientId = row.client;

  // avoid reacting to clicks on pager rows
  if (row.isPagerRow) {
    return;
  }

  setTimeout(() => {
    if (row.isGroup && clientId) {
      setExpandedGroups((prev) => {
        const currentlyExpanded = prev[clientId] ?? false;
        const willExpand = !currentlyExpanded;

        // ✅ Only clear when transitioning from collapsed -> expanded
        if (!currentlyExpanded && willExpand && typeof onClearSelectedData === 'function') {
          onClearSelectedData();
        }

        const newState: Record<string, boolean> = {};
        clientList.forEach((c) => {
          newState[c.client] = false;
        });
        newState[clientId] = willExpand;
        return newState;
      });

      // reset sysPrin page when switching group
      setSysPrinPageByClient((prev) => ({
        ...prev,
        [clientId]: 0,
      }));

      setTimeout(() => {
        if (onRowClick) onRowClick({ ...row });
        if (onFetchGroupDetails) onFetchGroupDetails(clientId);
      }, 0);
    } else if (!row.isGroup && clientId) {
      // ✅ Clicking a sysPrin row will NO LONGER clear selectedData
      setTimeout(() => {
        if (onRowClick) onRowClick(row);
      }, 0);
    }
  }, 0);
};
