onDataChange={(nextSelectedGroupRow: any) => {
  setSelectedGroupRow((prev) => {
    if (!prev) return nextSelectedGroupRow;

    const a = prev?.reportOptions ?? [];
    const b = nextSelectedGroupRow?.reportOptions ?? [];

    // ✅ 1) total 也算变化
    const prevTotal = Number(prev?.reportOptionTotal ?? 0);
    const nextTotal =
      nextSelectedGroupRow?.reportOptionTotal !== undefined
        ? Number(nextSelectedGroupRow.reportOptionTotal)
        : prevTotal;

    const optionsChanged =
      a.length !== b.length ||
      a.some((x: any, i: number) => {
        const y = b[i] || {};
        const xid = x.reportDetails?.reportId ?? x.reportId ?? null;
        const yid = y.reportDetails?.reportId ?? y.reportId ?? null;
        return (
          (x.reportDetails?.queryName ?? '') !== (y.reportDetails?.queryName ?? '') ||
          xid !== yid ||
          !!x.receiveFlag !== !!y.receiveFlag ||
          Number(x.outputTypeCd ?? 0) !== Number(y.outputTypeCd ?? 0) ||
          Number(x.fileTypeCd ?? 0) !== Number(y.fileTypeCd ?? 0) ||
          Number(x.emailFlag ?? 0) !== Number(y.emailFlag ?? 0) ||
          (x.reportPasswordTx ?? '') !== (y.reportPasswordTx ?? '') ||
          (x.emailBodyTx ?? '') !== (y.emailBodyTx ?? '')
        );
      });

    const totalChanged = prevTotal !== nextTotal;

    // ✅ 2) 如果 options 和 total 都没变，才 return
    if (!optionsChanged && !totalChanged) return prev;

    // ✅ 3) 合并：reportOptions + reportOptionTotal 都要写回 parent
    const merged = {
      ...prev,
      ...(nextSelectedGroupRow || {}),
      reportOptions: b,                 // 继续用 b
      reportOptionTotal: nextTotal,     // ✅ 写回
    };

    onClientUpdated?.(merged);
    return merged;
  });
}}
