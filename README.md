onDeleted={(deleted: AtmCashPrefixRow) => {
  const nextTotal = Math.max(0, totalCount - 1);

  setLocalCountAdjustment((prev) => prev - 1);
  notifyParentTotal(nextTotal);

  // ✅ optimistic remove from current page if it's there
  setPrefixes((prev) => prev.filter(p => !(p.billingSp === deleted.billingSp && p.prefix === deleted.prefix)));

  // ✅ clamp page if out of range
  const nextPageCount = Math.ceil(nextTotal / PAGE_SIZE); // could be 0
  const maxPageIdx = Math.max(0, nextPageCount - 1);
  setPage((p) => Math.min(p, maxPageIdx));

  // refresh page from server (keeps server truth)
  setRefreshKey((prev) => prev + 1);
}}
