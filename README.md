        <FormControl fullWidth>
            <label style={{ fontSize: '0.78rem', marginBottom: '4px' }}>Phone</label>
            <Box sx={{ display: 'flex', gap: 1 }}>
              {/* Country Code */}
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
      '& .MuiInputBase-root': { height: 25, fontSize: '0.78rem' },

      // Tighten the actual input/select padding to truly reduce height
      '& .MuiOutlinedInput-input': {
        paddingTop: '1px',
        paddingBottom: '1px',
        paddingLeft: '8px',
        paddingRight: '22px', // room for icon
        fontSize: '0.76rem',
        lineHeight: 1.1,
      },
      '& .MuiSelect-select': {
        paddingTop: '1px',
        paddingBottom: '1px',
        minHeight: 0,          // allow smaller than default
        fontSize: '0.76rem',
        lineHeight: 1.1,
      },

      // Make the dropdown icon a bit smaller & centered
      '& .MuiSelect-icon': {
        fontSize: '0,95rem',
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
                
                  <MenuItem value="+1">
                      <ReactCountryFlag countryCode="US" svg style={{ width: '1em', height: '1em', marginRight: 6 }} />
                      US
                    </MenuItem>
                    <MenuItem value="+1">
                      <ReactCountryFlag countryCode="CA" svg style={{ width: '1em', height: '1em', marginRight: 6 }} />
                      CA
                    </MenuItem>
                    <MenuItem value="+44">
                      <ReactCountryFlag countryCode="GB" svg style={{ width: '1em', height: '1em', marginRight: 6 }} />
                      UK
                    </MenuItem>
                    <MenuItem value="+61">
                      <ReactCountryFlag countryCode="AU" svg style={{ width: '1em', height: '1em', marginRight: 6 }} />
                      AU
                    </MenuItem>
                    <MenuItem value="+91">
                      <ReactCountryFlag countryCode="IN" svg style={{ width: '1em', height: '1em', marginRight: 6 }} />
                      IN
                    </MenuItem>
                </Select>
              </FormControl>
