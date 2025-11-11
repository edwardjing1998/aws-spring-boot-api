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
                    width: 70,
                    '& .MuiInputBase-root': {
                      height: '36px',
                      fontSize: '0.78rem',
                    },
                    '& .MuiSelect-select': {
                      paddingY: '6px',
                      fontSize: '0.78rem',
                    },
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
