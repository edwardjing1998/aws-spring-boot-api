<TableHead
  sx={{
    // apply to all header cells
    '& .MuiTableCell-root': {
      borderTop: 0,
      borderLeft: 0,
      borderRight: 0,
      borderBottom: '1px solid',
      borderColor: 'divider',
    },
  }}
>
  <TableRow sx={{ '& th': compactCellSx }}>
    <TableCell sx={{ ...compactCellSx, ...font78 }}>
      <span style={{ color: 'red' }}>Do Not Deliver to ...</span>
    </TableCell>

    <TableCell sx={{ ...compactCellSx, ...font78 }} align="right">
      <IconButton
        aria-label="Add area"
        onClick={handleAddArea}
        disabled={!isEditable}
        sx={{
          border: '1px solid #1976d2',
          bgcolor: '#fff',
          color: '#1976d2',
          p: 0.5,
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
  </TableRow>
</TableHead>
