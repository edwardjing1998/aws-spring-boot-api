sx={{
  ...sharedSx,
  width: 90,

  // Keep the whole control short
  '& .MuiOutlinedInput-root': {
    height: 24,           // ↓ from 25
    minHeight: 24,
  },

  // Tighten the real input area
  '& .MuiOutlinedInput-input': {
    paddingTop: 0,
    paddingBottom: 0,
    paddingLeft: 8,
    paddingRight: 20,     // room for chevron
    fontSize: '0.74rem',
    lineHeight: 1.0,      // compact
  },

  // Select-specific tightening
  '& .MuiSelect-select': {
    paddingTop: 0,
    paddingBottom: 0,
    minHeight: 0,
    fontSize: '0.74rem',
    lineHeight: 1.0,
  },

  // Smaller chevron, centered better for the short height
  '& .MuiSelect-icon': {
    fontSize: '0.9rem',   // <-- fix decimal
    right: 6,
  },
}}
