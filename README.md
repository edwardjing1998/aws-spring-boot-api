sx={{
  ...sharedSx,
  width: 90,

  '& .MuiOutlinedInput-root': {
    height: 24,
    minHeight: 24,
  },

  '& .MuiOutlinedInput-input': {
    paddingTop: 0,
    paddingBottom: 0,
    paddingLeft: 8,
    paddingRight: 22,      // leave room for icon
    fontSize: '0.74rem',
    lineHeight: 1.0,
  },

  '& .MuiSelect-select': {
    paddingTop: 0,
    paddingBottom: 0,
    minHeight: 0,
    fontSize: '0.74rem',
    lineHeight: 1.0,
  },

  // 🔽 true vertical centering for the chevron
  '& .MuiSelect-icon': {
    fontSize: '0.9rem',
    right: 6,
    top: '50%',
    transform: 'translateY(-50%)',
  },
  // some MUI themes use this class; include it to be safe
  '& .MuiSelect-iconOutlined': {
    top: '50%',
    transform: 'translateY(-50%)',
  },
}}
