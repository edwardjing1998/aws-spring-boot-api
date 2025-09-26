<Dialog
  open={open}
  onClose={handleClose}
  PaperProps={{
    className: 'client-report-mapping-dialog',
    sx: {
      width: 520,
      maxWidth: 'calc(100vw - 40px)',
      height: '50vh',          // ⬅️ 50% of viewport height
      maxHeight: '50vh',       // ⬅️ enforce the cap
      borderRadius: 2,
      display: 'flex',         // ⬅️ make content flex so it can scroll
      flexDirection: 'column',
      overflow: 'hidden',
    },
  }}
>
  <DialogTitle
    sx={{
      pr: 6,
      py: 1,
      bgcolor: '#0d6efd',   // ⬅️ blue header
      color: '#fff',
      fontWeight: 600,
    }}
  >
    Client Report Mapping
    <IconButton
      aria-label="close"
      onClick={handleClose}
      size="small"
      sx={{ position: 'absolute', right: 8, top: 8, color: '#fff' }}
    >
      <CloseIcon />
    </IconButton>
  </DialogTitle>

  <DialogContent
    dividers
    sx={{
      p: 1.5,
      overflow: 'auto',     // ⬅️ content scrolls within 50vh
    }}
  >
    {/* ... form stays the same ... */}
  </DialogContent>

  <DialogActions sx={{ px: 2, py: 1 }}>
    <div className="client-report-mapping-button-container">
      <Button variant="outlined" size="small" onClick={handleClose}>
        Cancel
      </Button>
      <Button variant="contained" size="small" disabled={!canSave()} onClick={handleSave}>
        Save
      </Button>
    </div>
  </DialogActions>
</Dialog>
