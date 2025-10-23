{/* Do Not Deliver grid table — scrollable container (no heading) */}
<TableContainer
  component={Paper}
  variant="outlined"
  sx={{ maxHeight: 180, overflowY: 'auto' }} // fixed height + scroll
>
  <Table size="small" stickyHeader aria-label="Do Not Deliver table">
    <TableHead>
      <TableRow>
        <TableCell sx={font78}>Area</TableCell>
        <TableCell sx={font78} align="right">Action</TableCell>
      </TableRow>
    </TableHead>
    <TableBody>
      {selectedInvalidAreas.length === 0 ? (
        <TableRow>
          <TableCell sx={font78} colSpan={2}>
            <em>No areas selected.</em>
          </TableCell>
        </TableRow>
      ) : (
        selectedInvalidAreas.map((name, idx) => (
          <TableRow key={`${name}-${idx}`}>
            <TableCell sx={font78}>{name}</TableCell>
            <TableCell align="right">
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
