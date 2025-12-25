<Button
  variant="text"
  size="small"
  sx={{ fontSize: '0.7rem', padding: '2px 8px', minWidth: 'unset', textTransform: 'none' }}
  onClick={() => {
    const nextPageCount = totalCount ? Math.ceil(totalCount / PAGE_SIZE) : 0;
    const maxPageIdx = Math.max(0, nextPageCount - 1);
    setClientPage(Math.min(page + 1, maxPageIdx));
  }}
  disabled={totalCount !== undefined ? page >= pageCount - 1 : reports.length < PAGE_SIZE}
>
  Next ▶
</Button>
