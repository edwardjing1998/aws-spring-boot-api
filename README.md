<CCard style={{ height: '35px', marginBottom: '4px', marginTop: '2px', border: 'none', borderBottom: '1px solid #ccc', boxShadow: 'none', borderRadius: '0px' }}>
        <CCardBody className="d-flex align-items-center" style={{ padding: '0.25rem 0.5rem', height: '100%', backgroundColor: 'transparent' }}>
          <CRow style={{ marginBottom: '20px' }}>
            {/* Address Line */}
            <CCol xs="6">
              <FormControl fullWidth>
                <label
                  htmlFor="addr-input"
                  style={{
                    fontSize: '0.78rem',
                    marginBottom: '4px',
                    display: 'flex',
                    alignItems: 'center',
                    gap: 6,
                  }}
                >
                  Address Line
                  <span
                    id="addr-counter"
                    style={{ fontSize: '0.72rem', color: addrLen >= MAX.addr ? '#d32f2f' : 'gray' }}
                  >
                    ({addrLen}/{MAX.addr})
                  </span>
                </label>

                <TextField
                  id="addr-input"
                  label=""
                  value={selectedGroupRow.addr || ''}
                  onChange={handleChange('addr')}
                  size="small"
                  fullWidth
                  disabled={!isEditable}
                  sx={sharedSx}
                  inputProps={{ maxLength: MAX.addr, 'aria-describedby': 'addr-counter' }}
                />
              </FormControl>
            </CCol>

            {/* City */}
            <CCol xs="2">
              <FormControl fullWidth>
                <label
                  htmlFor="city-input"
                  style={{
                    fontSize: '0.78rem',
                    marginBottom: '4px',
                    display: 'flex',
                    alignItems: 'center',
                    gap: 6,
                  }}
                >
                  City
                  <span
                    id="city-counter"
                    style={{
                      fontSize: '0.72rem',
                      color: cityLen >= MAX.city ? '#d32f2f' : 'gray',
                    }}
                  >
                    ({cityLen}/{MAX.city})
                  </span>
                </label>

                <TextField
                  id="city-input"
                  label=""
                  value={selectedGroupRow.city || ''}
                  onChange={handleChange('city')}
                  size="small"
                  fullWidth
                  disabled={!isEditable}
                  sx={sharedSx}
                  inputProps={{ maxLength: MAX.city, 'aria-describedby': 'city-counter' }}
                />
              </FormControl>
            </CCol>

            {/* State */}
            <CCol xs="2">
              <FormControl fullWidth>
                <label style={{ fontSize: '0.78rem', marginBottom: 4 }}>State</label>
                <TextField
                    select
                    label=""
                    value={selectedGroupRow.state || ''}
                    onChange={(e) =>
                      setSelectedGroupRow(prev => ({ ...(prev ?? {}), state: e.target.value }))
                    }
                    size="small"
                    fullWidth
                    disabled={!isEditable}
                    sx={sharedSx}
                    SelectProps={{
                      MenuProps: {
                        // ↓ smaller font just for the menu items
                        sx: {
                          '& .MuiMenuItem-root': {
                            fontSize: '0.72rem',  // tweak to taste
                            minHeight: 30,
                            lineHeight: 1.2,
                          },
                        },
                        PaperProps: { style: { maxHeight: 320 } },
                      },
                    }}
                  >
                    {US_STATES.map((s) => (
                      <MenuItem key={s.code} value={s.code}>
                        {s.name} ({s.code})
                      </MenuItem>
                    ))}
                  </TextField>
              </FormControl>

            </CCol>

            {/* Zip */}
            <CCol xs="2">
              <FormControl fullWidth>
                <label
                  htmlFor="zip-input"
                  style={{ fontSize: '0.78rem', marginBottom: '4px', display: 'flex', alignItems: 'center', gap: 6 }}
                >
                  Zip Code
                  <span
                    id="zip-counter"
                    style={{ fontSize: '0.72rem', color: zipLen >= MAX.zip ? '#d32f2f' : 'gray' }}
                  >
                    ({zipLen}/{MAX.zip})
                  </span>
                </label>

                <TextField
                  id="zip-input"
                  value={selectedGroupRow.zip || ''}
                  onChange={handleChange('zip')}
                  size="small"
                  fullWidth
                  disabled={!isEditable}
                  sx={sharedSx}
                  variant="outlined"
                  label=""
                  inputProps={{
                    maxLength: MAX.zip,
                    'aria-describedby': 'zip-counter',
                    inputMode: 'numeric', // optional UX hint on mobile keyboards
                  }}
                />
              </FormControl>
            </CCol>
          </CRow>
          </CCardBody>
      </CCard>
