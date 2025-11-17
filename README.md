@coreui/icons

          <FormControl fullWidth>
            <label style={{ fontSize: '0.78rem', marginBottom: '4px' }}>Phone</label>

            <Box sx={{ display: 'flex', gap: 1 }}>
              {/* Country Code — like State select */}
              <TextField
                select
                value={selectedGroupRow.phoneCountryCode || '+1'}
                onChange={handleChange('phoneCountryCode')}
                size="small"
                disabled={!isEditable}
                sx={{
                    ...sharedSx,
                    width: 90,

                    // match other fields (30px tall)
                    '& .MuiOutlinedInput-root': {
                      height: 30,
                      minHeight: 30,
                    },
                    '& .MuiInputBase-root': {
                      height: 30,                 // keep for consistency
                    },

                    // let the text vertically center within 30px
                    '& .MuiSelect-select': {
                      minHeight: 0,
                      paddingTop: 4,              // was 0
                      paddingBottom: 4,           // was 0
                      lineHeight: '30px',         // match container height
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
                        minHeight: 30,
                        lineHeight: 1.2,
                      },
                    },
                    PaperProps: { style: { maxHeight: 280 } },
                  },
                }}
              >
                {DIAL_CODES.map(opt => (
                  <MenuItem key={opt.value} value={opt.value}>
                    {opt.label}
                  </MenuItem>
                ))}
              </TextField>
