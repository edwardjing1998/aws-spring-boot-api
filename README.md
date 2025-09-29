<DialogTitle
  sx={{
    position: 'relative',
    display: 'flex',
    alignItems: 'center',   // ← centers “Confirm Delete” vertically
    pr: 5,
    pl: 2,
    py: 0.5,
    minHeight: 34,
    bgcolor: '#0d6efd',
    color: 'white',
    fontSize: 14,
    lineHeight: 1.2,
    fontWeight: 600,
  }}
>
  <span style={{ flex: 1 }}>Confirm Delete</span>

  <IconButton
    aria-label="close"
    onClick={closeDeleteDialog}
    size="small"
    sx={{
      position: 'absolute',
      right: 6,
      top: 4,
      p: 0.25,
      '& svg': { width: 16, height: 16 },
    }}
  >
    <CloseIcon />
  </IconButton>
</DialogTitle>
