const notifyParentTotal = (newTotal: number) => {
  if (!clientId) return;

  // send a full updated group row so parent can set state directly
  onDataChange?.({
    ...(selectedGroupRow || {}),
    clientId,
    reportOptionTotal: Math.max(0, newTotal),
  } as any);
};
