sx={{
  ...sharedSx,
  width: 70,

  // shorter control
  '& .MuiOutlinedInput-root': {
    height: 24,       // ↓ was 36
    minHeight: 24,
  },

  // tighten the inner padding so the visual height matches
  '& .MuiOutlinedInput-input': {
    paddingTop: 0,
    paddingBottom: 0,
    paddingLeft: 8,
    paddingRight: 20,
    fontSize: '0.78rem',
    lineHeight: 1.0,
  },

  // select-specific
  '& .MuiSelect-select': {
    paddingTop: 0,
    paddingBottom: 0,
    minHeight: 0,
    fontSize: '0.78rem',
    lineHeight: 1.0,
  },

  // (optional) keep the chevron centered for short height
  '& .MuiSelect-icon, & .MuiSelect-iconOutlined': {
    top: '50%',
    transform: 'translateY(-50%)',
  },
}}
