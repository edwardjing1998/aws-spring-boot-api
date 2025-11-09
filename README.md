<EditClientReport
  selectedGroupRow={viewRow}
  isEditable={isEditable}
  onDataChange={(nextSelectedGroupRow) => {
    setSelectedGroupRow((prev) => {
      if (!prev) return nextSelectedGroupRow;

      const a = prev?.reportOptions ?? [];
      const b = nextSelectedGroupRow?.reportOptions ?? [];

      // shallow-ish deep compare that’s good enough for your row structure
      const sameReports =
        a.length === b.length &&
        a.every((x, i) => {
          const y = b[i] || {};
          return (
            (x.reportDetails?.queryName ?? '') === (y.reportDetails?.queryName ?? '') &&
            (x.reportDetails?.reportId ?? x.reportId ?? null) === (y.reportDetails?.reportId ?? y.reportId ?? null) &&
            !!x.receiveFlag === !!y.receiveFlag &&
            Number(x.outputTypeCd ?? 0) === Number(y.outputTypeCd ?? 0) &&
            Number(x.fileTypeCd ?? 0) === Number(y.fileTypeCd ?? 0) &&
            Number(x.emailFlag ?? 0) === Number(y.emailFlag ?? 0) &&
            (x.reportPasswordTx ?? '') === (y.reportPasswordTx ?? '') &&
            (x.emailBodyTx ?? '') === (y.emailBodyTx ?? '')
          );
        });

      // also ensure we’re not changing the identity fields unnecessarily
      const sameIdBlock =
        (prev.client ?? prev.clientId ?? prev.billingSp ?? '') ===
        (nextSelectedGroupRow.client ?? nextSelectedGroupRow.clientId ?? nextSelectedGroupRow.billingSp ?? '');

      if (sameReports && sameIdBlock) {
        // No meaningful change — return prev to avoid a re-render loop
        return prev;
      }
      return nextSelectedGroupRow;
    });
  }}
/>
