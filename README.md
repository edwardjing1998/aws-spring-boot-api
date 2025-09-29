<DialogTitle
  sx={{
    bgcolor: '#0d6efd',
    color: 'white',
    // ↓ shrink height & text
    fontSize: '0.9rem',     // was default ~1.25rem (h6)
    lineHeight: 1.2,
    py: 0.5,                // vertical padding (theme spacing units) — try 0.25~0.75
    pl: 1.25,
    pr: 5,                  // leave room for the close button
    minHeight: 30,          // guardrail for very small padding
    position: 'relative',
  }}
>
  {title}
  <IconButton
    aria-label="close"
    onClick={handleClose}
    size="small"
    sx={{
      position: 'absolute',
      right: 6,
      top: 4,               // you can nudge this up/down as you like
      p: 0.25,
      width: 26,
      height: 26,
      '& svg': { width: 16, height: 16 },
    }}
  >
    <CloseIcon />
  </IconButton>
</DialogTitle>
