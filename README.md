<FormControl sx={{ minWidth: 70 }} size="small">
  <Select
    value={selectedGroupRow.phoneCountryCode || '+1'}
    onChange={handleChange('phoneCountryCode')}
    disabled={!isEditable}
    displayEmpty
    sx={{
      ...sharedSx,
      width: 70,
      '& .MuiInputBase-root': { height: '36px', fontSize: '0.74rem' },   // field font
      '& .MuiSelect-select':   { paddingY: '6px',  fontSize: '0.74rem' }, // selected value font
    }}
    MenuProps={{
      sx: {
        '& .MuiMenuItem-root': {
          fontSize: '0.72rem',     // ↓ menu item text smaller
          minHeight: 28,
          lineHeight: 1.2,
        },
      },
      PaperProps: { style: { maxHeight: 280 } },
    }}
  >
    <MenuItem value="+1">
      <ReactCountryFlag countryCode="US" svg style={{ width: '0.9em', height: '0.9em', marginRight: 6 }} />
      US
    </MenuItem>
    <MenuItem value="+1">
      <ReactCountryFlag countryCode="CA" svg style={{ width: '0.9em', height: '0.9em', marginRight: 6 }} />
      CA
    </MenuItem>
    <MenuItem value="+44">
      <ReactCountryFlag countryCode="GB" svg style={{ width: '0.9em', height: '0.9em', marginRight: 6 }} />
      UK
    </MenuItem>
    <MenuItem value="+61">
      <ReactCountryFlag countryCode="AU" svg style={{ width: '0.9em', height: '0.9em', marginRight: 6 }} />
      AU
    </MenuItem>
    <MenuItem value="+91">
      <ReactCountryFlag countryCode="IN" svg style={{ width: '0.9em', height: '0.9em', marginRight: 6 }} />
      IN
    </MenuItem>
  </Select>
</FormControl>
