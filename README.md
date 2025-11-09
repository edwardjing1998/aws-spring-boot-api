<EditClientReport
  selectedGroupRow={viewRow}
  isEditable={isEditable}
  onDataChange={(nextSelectedGroupRow) => {
    setSelectedGroupRow((prev) => {
      if (!prev) return nextSelectedGroupRow;

      const a = prev?.reportOptions ?? [];
      const b = nextSelectedGroupRow?.reportOptions ?? [];

      const changed =
        a.length !== b.length ||
        a.some((x, i) => {
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

      if (!changed) return prev;         // no-op: avoid loops
      const merged = { ...prev, reportOptions: b };
      // also bubble to the page (if it listens here)
      onClientUpdated?.(merged);
      return merged;
    });
  }}
/>
