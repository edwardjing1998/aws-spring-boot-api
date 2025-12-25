<EditAtmCashPrefix
  selectedGroupRow={viewRow}
  onDataChange={(nextSelectedGroupRow: any) => {
    setSelectedGroupRow((prev) => {
      if (!prev) return nextSelectedGroupRow;

      const b =
        nextSelectedGroupRow?.sysPrinsPrefixes ??
        prev.sysPrinsPrefixes ??
        [];

      const rawTotal =
        nextSelectedGroupRow?.clientPrefixTotal !== undefined
          ? nextSelectedGroupRow.clientPrefixTotal
          : (prev?.clientPrefixTotal ?? 0);

      const nextTotal = Number.isFinite(Number(rawTotal)) ? Number(rawTotal) : 0;

      const merged = {
        ...prev,

        // ✅ 只更新这两个：列表 + total
        sysPrinsPrefixes: b,
        clientPrefixTotal: nextTotal,

        // ✅（可选）允许 child 纠正这两个标识字段（有就用，没有就保留）
        billingSp: nextSelectedGroupRow?.billingSp ?? prev.billingSp,
        client: nextSelectedGroupRow?.client ?? prev.client,
      };

      onClientUpdated?.(merged);
      return merged;
    });
  }}
/>
