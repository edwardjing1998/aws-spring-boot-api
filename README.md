onCreated={(created: AtmCashPrefixRow) => {
  // ✅ 新增前：当前 totalCount 对应的最后页
  const beforeLastPageIdx = Math.max(0, Math.ceil(totalCount / PAGE_SIZE) - 1);

  // ✅ 新增后 total
  const nextTotal = Math.max(0, totalCount + 1);

  // optimistic local total update
  setLocalCountAdjustment((prev) => prev + 1);

  // ✅ update parent immediately
  notifyParentTotal(nextTotal);

  const normalized = { ...created, atmCashRule: String(created.atmCashRule ?? '') };

  // ✅ 只有当“新增前我就在最后页”，才做你原来的自动追加/自动翻页
  const wasOnLastPageBeforeAdd = page === beforeLastPageIdx;

  if (wasOnLastPageBeforeAdd) {
    if (prefixes.length < PAGE_SIZE) {
      // 当前页还有空位：直接 append
      setPrefixes((prev) => [...prev, normalized]);
    } else {
      // 当前页满了：跳到下一页（新增后一定存在这个页）
      setPage(page + 1);
      setPrefixes([normalized]); // optimistic next page data
    }
  } else {
    // 不在最后页：不改变当前视图，必要时你也可以 refreshKey++ 保持和服务端一致
    // setRefreshKey((prev) => prev + 1);
  }

  setSelectedPrefix(normalized.prefix);
}}
