<TableContainer
  component={Paper}
  variant="outlined"
  sx={{
    height: 120,            // <- fixed height (not maxHeight)
    overflowY: 'auto',
    scrollbarWidth: 'thin',
    scrollbarColor: '#cfd8dc #f5f7fa',
    '&::-webkit-scrollbar': { width: 8, height: 8 },
    '&::-webkit-scrollbar-track': { backgroundColor: '#f5f7fa', borderRadius: 8 },
    '&::-webkit-scrollbar-thumb': { backgroundColor: '#cfd8dc', borderRadius: 8, border: '2px solid #f5f7fa' },
    '&::-webkit-scrollbar-thumb:hover': { backgroundColor: '#bfcbd3' },
  }}
>
  <Table size="small" stickyHeader aria-label="Do Not Deliver table">
    <TableHead>
      <TableRow sx={{ '& th': compactCellSx }}>
        <TableCell sx={{ ...compactCellSx, ...font78 }}>
          <span style={{ color: 'red' }}>Do Not Deliver to ...</span>
        </TableCell>
        <TableCell sx={{ ...compactCellSx, ...font78 }} align="right">
          {/* your New/+ button here */}
        </TableCell>
      </TableRow>
    </TableHead>

    <TableBody>
      {selectedInvalidAreas.length === 0 ? (
        <>
          <TableRow sx={{ '& td': compactCellSx }}>
            <TableCell sx={{ ...compactCellSx, ...font78 }} colSpan={2}>
              <em>No areas selected.</em>
            </TableCell>
          </TableRow>

          {/* filler row to keep body height even when empty */}
          <TableRow>
            <TableCell colSpan={2} sx={{ p: 0, border: 0 }}>
              <div style={{ height: 80 }} /> {/* adjust to taste */}
            </TableCell>
          </TableRow>
        </>
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
