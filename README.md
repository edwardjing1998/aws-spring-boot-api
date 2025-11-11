             <FormControl sx={{ minWidth: 90 }} size="small">
                <Select
                  value={selectedGroupRow.phoneCountryCode || '+1'}
                  onChange={handleChange('phoneCountryCode')}
                  disabled={!isEditable}
                  displayEmpty
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

              {/* Number */}
                  <Box sx={{
                      display: 'flex',
                      gap: 1,
                      alignItems: 'flex-start',
                      flex: 1,
                      maxWidth: 'calc(100% - 98px)', // 90 (select) + 8 (gap=1)
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
