export const REPORT_BREAK_OPTIONS = [
    { value: '0', label: 'System' },
    { value: '1', label: 'Sys/Prin' }
  ];


              <CCol xs="3">
              <FormControl fullWidth size="small" sx={sharedSx}>
                <label style={{ fontSize: '0.78rem', marginBottom: '4px' }}>
                  Report Breaks
                </label>
                <Select
                  value={selectedGroupRow.reportBreakFlag?.toString() || ''}
                  onChange={handleChange('reportBreakFlag')}
                  disabled={!isEditable}
                  displayEmpty
                  sx={{
                    ...sharedSx,
                    '& .MuiSelect-select': {
                      display: 'flex',
                      alignItems: 'center',
                      lineHeight: '1rem',
                      fontSize: '0.78rem',
                      minHeight: '36px',
                    },
                    '& .MuiInputBase-root': { height: '36px' },
                  }}
                >
                  <MenuItem value="" sx={{ fontSize: '0.78rem' }}>
                    None
                  </MenuItem>
                  <MenuItem value="0" sx={{ fontSize: '0.78rem' }}>
                    System
                  </MenuItem>
                  <MenuItem value="1" sx={{ fontSize: '0.78rem' }}>
                    Sys/Prin
                  </MenuItem>
                </Select>
              </FormControl>
            </CCol>
