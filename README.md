<Button
  variant="text"
  size="small"
  sx={{ fontSize: '0.7rem', padding: '2px 8px', minWidth: 'unset', textTransform: 'none' }}
  onClick={() => {
    const minPageIdx = 0;
    setClientPage(Math.max(page - 1, minPageIdx));
  }}
  disabled={page === 0}
>
  ◀ Previous
</Button>
