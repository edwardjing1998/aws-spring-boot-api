import { Box } from '@mui/material'; // <-- add this import

// ...

<TableContainer
  component={Paper}
  variant="outlined"
  sx={{
    position: 'relative',        // <-- allow overlay positioning
    height: hasAreas ? 120 : 80,  // optional: shorter when empty
    overflowY: hasAreas ? 'auto' : 'hidden',
    scrollbarWidth: 'thin',
    scrollbarColor: '#cfd8dc #f5f7fa',
    '&::-webkit-scrollbar': { width: 8, height: 8 },
    '&::-webkit-scrollbar-track': { backgroundColor: '#f5f7fa', borderRadius: 8 },
    '&::-webkit-scrollbar-thumb': { backgroundColor: '#cfd8dc', borderRadius: 8, border: '2px solid #f5f7fa' },
    '&::-webkit-scrollbar-thumb:hover': { backgroundColor: '#bfcbd3' },
  }}
>
  <Table size="small" stickyHeader aria-label="Do Not Deliver table">
    <TableHead
      sx={{
        '& .MuiTableCell-root': {
          borderTop: 'none',
          borderLeft: 'none',
          borderRight: 'none',
          borderBottom: '1px solid',
          borderColor: 'divider',
        },
      }}
    >
      <TableRow sx={{ '& th': compactCellSx }}>
        <TableCell sx={{ ...compactCellSx, ...font78 }}>
          <span style={{ color: 'red' }}>Do Not Deliver to ...</span>
        </TableCell>
        <TableCell sx={{ ...compactCellSx }} align="right">
          <IconButton
            aria-label="Add area"
            onClick={handleAddArea}
            disabled={!isEditable}
            size="small"
            sx={{
              width: 22, height: 22, p: 0,
              border: '1px solid #1976d2', bgcolor: '#fff', color: '#1976d2',
              borderRadius: '6px',
              '&:hover': { bgcolor: '#e3f2fd' },
              '&.Mui-disabled': { borderColor: 'divider', color: 'action.disabled', bgcolor: 'transparent' },
            }}
          >
            <AddIcon sx={{ fontSize: 14 }} />
          </IconButton>
        </TableCell>
      </TableRow>
    </TableHead>

    <TableBody>
      {hasAreas ? (
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
      ) : null}
    </TableBody>
  </Table>

  {/* Centered empty-state overlay (not inside a TableCell) */}
  {!hasAreas && (
    <Box
      sx={{
        position: 'absolute',
        left: 0,
        right: 0,
        // push below header (tweak if your header height differs)
        top: 36,
        bottom: 0,
        display: 'flex',
        alignItems: 'center',
        justifyContent: 'center',
        pointerEvents: 'none',         // clicks go through
      }}
    >
      <em style={{ fontSize: '0.73rem', color: 'rgba(0,0,0,0.6)' }}>
        No areas selected.
      </em>
    </Box>
  )}
</TableContainer>
