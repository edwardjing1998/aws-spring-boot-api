onCreated={(created: AtmCashPrefixRow) => {
  const nextTotal = Math.max(0, totalCount + 1);

  // optimistic local total update
  setLocalCountAdjustment((prev) => prev + 1);

  // ✅ update parent immediately
  notifyParentTotal(nextTotal);

  // ✅ compute last page based on nextTotal (NOT totalCount)
  const nextLastPageIdx = Math.max(0, Math.ceil(nextTotal / PAGE_SIZE) - 1);

  const normalized = { ...created, atmCashRule: String(created.atmCashRule ?? '') };

  // Keep your existing “auto paging” behavior, but correct:
  const isLastPageNow = page === nextLastPageIdx;

  if (isLastPageNow) {
    if (prefixes.length < PAGE_SIZE) {
      setPrefixes((prev) => [...prev, normalized]);
    } else {
      // Page full => go to next page
      setPage(page + 1);
      setPrefixes([normalized]); // optimistic next page data
    }
  }

  setSelectedPrefix(created.prefix);
}}
