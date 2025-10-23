<TableCell sx={{ ...compactCellSx }} align="right">
  <IconButton
    aria-label="Add area"
    onClick={handleAddArea}
    disabled={!isEditable}
    sx={{
      border: '1px solid #1976d2',   // blue border
      bgcolor: '#fff',               // white background
      color: '#1976d2',              // blue icon
      p: 0.5,                        // compact
      '&:hover': { bgcolor: '#e3f2fd' },
      '&.Mui-disabled': {
        borderColor: 'divider',
        color: 'action.disabled',
        bgcolor: 'transparent',
      },
    }}
  >
    <AddIcon fontSize="small" />
  </IconButton>
</TableCell>
