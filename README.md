  // ✅ Helper: tell parent the new total (so pagination stays correct after add/delete)
  const notifyParentTotal = (newTotal: number) => {
    if (!clientId) return;

    onDataChange?.({
      ...(selectedGroupRow || {}),
      clientId,
      clientPrefixTotal: Math.max(0, newTotal),
    } as any);
  };
