onDeleted={(_deleted: AtmCashPrefixRow) => {
  const nextTotal = Math.max(0, totalCount - 1);

  setLocalCountAdjustment((prev) => prev - 1);

  // ✅ update parent immediately
  notifyParentTotal(nextTotal);

  // ✅ if current page becomes out of range after deletion, clamp it
  const nextPageCount = Math.ceil(nextTotal / PAGE_SIZE); // could be 0
  const maxPageIdx = Math.max(0, nextPageCount - 1);
  setPage((p) => Math.min(p, maxPageIdx));

  // refresh page from server
  setRefreshKey((prev) => prev + 1);
}}
