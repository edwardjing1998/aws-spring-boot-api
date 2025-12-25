<EditAtmCashPrefix
  selectedGroupRow={viewRow}
  onDataChange={(nextSelectedGroupRow: any) => {
    setSelectedGroupRow((prev) => {
      if (!prev) return nextSelectedGroupRow;

      const merged = {
        ...prev,
        sysPrinsPrefixes: nextSelectedGroupRow?.sysPrinsPrefixes ?? prev.sysPrinsPrefixes,
        clientPrefixTotal: nextSelectedGroupRow?.clientPrefixTotal ?? prev.clientPrefixTotal, // ✅ 必加
      };

      onClientUpdated?.(merged);
      return merged;
    });
  }}
/>
