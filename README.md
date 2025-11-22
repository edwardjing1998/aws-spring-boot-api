            <CCol xs="3">
              <FormControl fullWidth size="small" sx={sharedSx}>
                <label style={{ fontSize: '0.78rem', marginBottom: '4px' }}>
                  Search Type
                </label>
                <Select
                  value={selectedGroupRow.chLookUpType?.toString() || ''}
                  onChange={handleChange('chLookUpType')}
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
                    Standard
                  </MenuItem>
                  <MenuItem value="1" sx={{ fontSize: '0.78rem' }}>
                    Amex-AS400
                  </MenuItem>
                  <MenuItem value="2" sx={{ fontSize: '0.78rem' }}>
                    AS400
                  </MenuItem>
                </Select>
              </FormControl>
            </CCol>



  
  export const SEARCH_TYPE_OPTIONS = [
    { value: '0', label: 'Standard' },
    { value: '1', label: 'Amex-AS400' },
    { value: '2', label: 'AS400' }
  ];







            
