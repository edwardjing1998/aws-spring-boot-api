onClick={() => {
  const nextPageCount = totalCount ? Math.ceil(totalCount / PAGE_SIZE) : 0;
  const maxPageIdx = Math.max(0, nextPageCount - 1);
  setPage(Math.min(page + 1, maxPageIdx));
}}
