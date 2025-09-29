<DialogTitle
  sx={{
    pr: 5,              // a bit less right padding to make room for the close icon
    py: 0.5,            // ↓ header height (was 1.25)
    minHeight: 34,      // optional hard cap for compact height
    bgcolor: '#0d6efd',
    color: 'white',
    fontSize: 14,       // ↓ smaller title text
    lineHeight: 1.2,    // tighter line height
    fontWeight: 600,    // (optional) make it readable at smaller size
  }}
>
  Confirm Delete
  <IconButton
    aria-label="close"
    onClick={closeDeleteDialog}
    size="small"
    sx={{
      position: 'absolute',
      right: 6,
      top: 4,           // pull closer to the top edge
      p: 0.25,          // smaller hit area
      '& svg': { width: 16, height: 16 }, // smaller icon
    }}
  >
    <CloseIcon />
  </IconButton>
</DialogTitle>
