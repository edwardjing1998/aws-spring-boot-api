<TableContainer
  component={Paper}
  variant="outlined"
  sx={{
    /* keep everything you already have */
    height: 120,
    overflowY: 'auto',
    scrollbarWidth: 'thin',
    scrollbarColor: '#cfd8dc #f5f7fa',
    '&::-webkit-scrollbar': { width: 8, height: 8 },
    '&::-webkit-scrollbar-track': { backgroundColor: '#f5f7fa', borderRadius: 8 },
    '&::-webkit-scrollbar-thumb': { backgroundColor: '#cfd8dc', borderRadius: 8, border: '2px solid #f5f7fa' },
    '&::-webkit-scrollbar-thumb:hover': { backgroundColor: '#bfcbd3' },

    /* just add this one line */
    position: 'relative',
  }}
>
  {/* your existing <Table> ... unchanged */}
  <Table size="small" stickyHeader aria-label="Do Not Deliver table">
    {/* ...TableHead, TableBody etc. exactly as you have now */}
  </Table>

  {/* NEW: centered overlay; does not change the table itself */}
  {!hasAreas && (
    <Box
      sx={{
        position: 'absolute',
        inset: 0,                // fill container
        display: 'flex',
        alignItems: 'center',
        justifyContent: 'center',
        pointerEvents: 'none',   // don’t block clicks on header/add button
      }}
    >
      <em style={{ fontSize: '0.73rem', color: 'rgba(0,0,0,0.6)' }}>
        No areas selected.
      </em>
    </Box>
  )}
</TableContainer>



import { Box } from '@mui/material';
const hasAreas = selectedInvalidAreas?.length > 0;
