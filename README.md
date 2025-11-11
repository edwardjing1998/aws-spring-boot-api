sx={{
  ...sharedSx,
  width: 70,

  // shorter control (works better than .MuiInputBase-root for Select)
  '& .MuiOutlinedInput-root': {
    height: 24,
    minHeight: 24,
    position: 'relative',           // for icon centering
  },

  // tighten inner padding so visual height matches
  '& .MuiOutlinedInput-input': {
    paddingTop: 0,
    paddingBottom: 0,
    paddingLeft: 8,
    paddingRight: 20,
    fontSize: '0.78rem',
    lineHeight: 1.0,
  },

  // select content tighter
  '& .MuiSelect-select': {
    paddingTop: 0,
    paddingBottom: 0,
    minHeight: 0,
    fontSize: '0.78rem',
    lineHeight: 1.0,
  },

  // keep chevron vertically centered for the short height
  '& .MuiSelect-icon, & .MuiSelect-iconOutlined': {
    right: 6,
    top: '50%',
    transform: 'translateY(-50%)',
  },
}}
