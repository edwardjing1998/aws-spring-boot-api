<Select
  /* ...props... */
  IconComponent={() => null}
  renderValue={(val) => (val ? (CCODES[val] ?? String(val)) : '')}
  sx={{
    ...sharedSx,
    width: 70,

    // Short control
    '& .MuiOutlinedInput-root': {
      height: 24,
      minHeight: 24,
      position: 'relative',
    },

    // Keep the input area tight
    '& .MuiOutlinedInput-input': {
      paddingTop: 0,
      paddingBottom: 0,
      paddingLeft: 8,
      paddingRight: 8,      // no chevron → smaller right pad
      fontSize: '0.78rem',
      // 👇 make the text sit vertically centered
      height: 24,
      lineHeight: '24px',
      display: 'flex',
      alignItems: 'center',
    },

    // Select-specific node (some themes target this)
    '& .MuiSelect-select': {
      paddingTop: 0,
      paddingBottom: 0,
      minHeight: 0,
      fontSize: '0.78rem',
      // 👇 also enforce centering here
      height: 24,
      lineHeight: '24px',
      display: 'flex',
      alignItems: 'center',
    },

    // Ensure any theme-provided chevron is hidden
    '& .MuiSelect-icon, & .MuiSelect-iconOutlined': { display: 'none' },
  }}
/>
