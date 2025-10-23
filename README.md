<TableCell sx={{ ...compactCellSx }} align="right">
  <IconButton
    aria-label="Add area"
    size="small"
    onClick={handleAddArea}
    disabled={!isEditable}
    sx={{
      backgroundColor: '#fff',     // white bg
      color: '#1976d2',            // blue icon
      border: '1px solid #1976d2', // blue border
      borderRadius: '6px',
      p: '2px',                    // compact
      '&:hover': {
        backgroundColor: '#f7fbff',
      },
    }}
  >
    <AddIcon fontSize="small" />
  </IconButton>
</TableCell>
