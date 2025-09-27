  const onRowClicked = (event) => {
    const row = event.data;
    if (row?.isGroup && row?.dateKey) {
      // 1) Clear focused cell so the grid won't try to restore it during your data change
      gridApiRef.current?.clearFocusedCell();
  
      // 2) Defer state change to the next macrotask so it won't run mid-render
      setTimeout(() => {
        setExpandedGroups(prev => ({
          ...prev,
          [row.dateKey]: !(prev[row.dateKey] ?? false),
        }));
      }, 0);
    }
  };
