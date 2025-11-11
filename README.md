<FormControl sx={{ minWidth: 90 }} size="small">
  <Select
    value={selectedGroupRow.phoneCountryCode || '+1'}
    onChange={handleChange('phoneCountryCode')}
    disabled={!isEditable}
    displayEmpty
    sx={{
      ...sharedSx,
      width: 90,

      // Root height (fallback)
      '& .MuiInputBase-root': { height: 30, fontSize: '0.78rem' },

      // Tighten the actual input/select padding to truly reduce height
      '& .MuiOutlinedInput-input': {
        paddingTop: '2px',
        paddingBottom: '2px',
        paddingLeft: '8px',
        paddingRight: '24px', // room for icon
        fontSize: '0.78rem',
        lineHeight: 1.2,
      },
      '& .MuiSelect-select': {
        paddingTop: '2px',
        paddingBottom: '2px',
        minHeight: 0,          // allow smaller than default
        fontSize: '0.78rem',
        lineHeight: 1.2,
      },

      // Make the dropdown icon a bit smaller & centered
      '& .MuiSelect-icon': {
        fontSize: '1rem',
        right: 6,
      },
    }}
    MenuProps={{
      sx: {
        '& .MuiMenuItem-root': { fontSize: '0.72rem', minHeight: 26, lineHeight: 1.2 },
      },
      PaperProps: { style: { maxHeight: 260 } },
    }}
  >
