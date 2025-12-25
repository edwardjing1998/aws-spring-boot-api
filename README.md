<EditAtmCashPrefix
  selectedGroupRow={viewRow}
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
        // ✅ 只更新你关心的两个字段
        sysPrinsPrefixes: b,
        clientPrefixTotal: nextTotal,

        // （可选）如果你希望 child 能修正 billingSp / client，也可以只挑这两个
        billingSp: nextSelectedGroupRow?.billingSp ?? prev.billingSp,
        client: nextSelectedGroupRow?.client ?? prev.client,
      };

      onClientUpdated?.(merged);
      return merged;
    });
  }}
/>
