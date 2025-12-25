onDataChange={(nextSelectedGroupRow: any) => {
  setSelectedGroupRow((prev) => {
    if (!prev) return nextSelectedGroupRow;

    const b = nextSelectedGroupRow?.sysPrinsPrefixes ?? prev.sysPrinsPrefixes ?? [];

    const nextTotal =
      nextSelectedGroupRow?.clientPrefixTotal !== undefined
        ? Number(nextSelectedGroupRow.clientPrefixTotal)
        : Number(prev?.clientPrefixTotal ?? 0);

    const merged = {
      ...prev,
      ...(nextSelectedGroupRow || {}),
      sysPrinsPrefixes: b,
      clientPrefixTotal: nextTotal, 
    };

    onClientUpdated?.(merged);
    return merged;
  });
}}
