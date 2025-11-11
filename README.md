sx={{
  ...sharedSx,
  width: 90,

  // match other fields (30px tall)
  '& .MuiOutlinedInput-root': {
    height: 30,
    minHeight: 30,
  },
  '& .MuiInputBase-root': {
    height: 30,                 // keep for consistency
  },

  // let the text vertically center within 30px
  '& .MuiSelect-select': {
    minHeight: 0,
    paddingTop: 4,              // was 0
    paddingBottom: 4,           // was 0
    lineHeight: '30px',         // match container height
    fontSize: '0.78rem',
    display: 'flex',
    alignItems: 'center',
  },
}}
