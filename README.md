<DialogTitle
  sx={{
    bgcolor: '#0d6efd',
    color: 'white',
    fontSize: '0.9rem',
    lineHeight: 1.2,
    pl: 1.25,
    pr: 5,
    pt: 1,          // ↑ push content down
    pb: 0.5,
    minHeight: 30,
    position: 'relative',
    '& .MuiTypography-root': {
      mt: 0.25,     // ↑ nudge the actual title text down a bit more
    },
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
      top: 4,
      p: 0.25,
      width: 26,
      height: 26,
      '& svg': { width: 16, height: 16 },
    }}
  >
    <CloseIcon />
  </IconButton>
</DialogTitle>
