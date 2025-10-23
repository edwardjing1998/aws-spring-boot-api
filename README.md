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



import { Box } from '@mui/material';
const hasAreas = selectedInvalidAreas?.length > 0;
