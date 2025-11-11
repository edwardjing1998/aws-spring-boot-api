<FormControl fullWidth>
  <label style={{ fontSize: '0.78rem', marginBottom: '4px' }}>Phone</label>

  <Box sx={{ display: 'flex', gap: 1 }}>
    {/* Country Code — TextField with select (same pattern as State) */}
    <TextField
      select
      value={selectedGroupRow.phoneCountryCode || '+1'}
      onChange={handleChange('phoneCountryCode')}
      size="small"
      disabled={!isEditable}
      sx={{
        ...sharedSx,
        width: 90,                         // fixed width like before
        '& .MuiInputBase-root': { height: '24px' }, // compact height
        '& .MuiSelect-select': {
          minHeight: 0,
          paddingTop: 0,
          paddingBottom: 0,
          lineHeight: '24px',
          fontSize: '0.78rem',
          display: 'flex',
          alignItems: 'center',
        },
      }}
      SelectProps={{
        displayEmpty: true,
        renderValue: (val) =>
          (DIAL_CODES.find(o => o.value === val)?.label || String(val || '')),
        MenuProps: {
          sx: {
            '& .MuiMenuItem-root': {
              fontSize: '0.72rem',
              minHeight: 26,
              lineHeight: 1.2,
            },
          },
          PaperProps: { style: { maxHeight: 260 } },
        },
      }}
    >
      {DIAL_CODES.map(opt => (
        <MenuItem key={opt.value} value={opt.value}>
          {opt.label}
        </MenuItem>
      ))}
    </TextField>

    {/* Number */}
    <Box
      sx={{
        display: 'flex',
        gap: 1,
        alignItems: 'flex-start',
        flex: 1,
        maxWidth: 'calc(100% - 98px)', // 90 (country code) + 8px gap
      }}
    >
      <TextField
        label=""
        value={selectedGroupRow.phone || ''}
        onChange={handleChange('phone')}
        size="small"
        fullWidth
        disabled={!isEditable}
        sx={sharedSx}
      />
    </Box>
  </Box>
</FormControl>
