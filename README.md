const compactCellSx = { py: 0.25, px: 1 }; // tighten vertical (py) & horizontal (px) padding

<TableContainer component={Paper} variant="outlined" sx={{ maxHeight: 180, overflowY: 'auto' }}>
  <Table size="small" stickyHeader aria-label="Do Not Deliver table">
    <TableHead>
      <TableRow sx={{ '& th': compactCellSx }}>
        <TableCell sx={{ ...compactCellSx, ...font78 }}>Area</TableCell>
        <TableCell sx={{ ...compactCellSx, ...font78 }} align="right">Action</TableCell>
      </TableRow>
    </TableHead>
    <TableBody>
      {selectedInvalidAreas.length === 0 ? (
        <TableRow sx={{ '& td': compactCellSx }}>
          <TableCell sx={{ ...compactCellSx, ...font78 }} colSpan={2}>
            <em>No areas selected.</em>
          </TableCell>
        </TableRow>
      ) : (
        selectedInvalidAreas.map((name, idx) => (
          <TableRow key={`${name}-${idx}`} sx={{ '& td': compactCellSx }}>
            <TableCell sx={{ ...compactCellSx, ...font78 }}>{name}</TableCell>
            <TableCell sx={{ ...compactCellSx }} align="right">
              <IconButton
                size="small"
                aria-label={`Delete ${name}`}
                onClick={() => handleDeleteArea(name)}
                disabled={!isEditable}
              >
                <DeleteIcon fontSize="small" />
              </IconButton>
            </TableCell>
          </TableRow>
        ))
      )}
    </TableBody>
  </Table>
</TableContainer>
