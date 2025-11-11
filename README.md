<FormControl fullWidth>
            <label style={{ fontSize: '0.78rem', marginBottom: '4px' }}>Zip Code</label>
            <TextField
              value={selectedGroupRow.zip || ''}
              onChange={handleChange('zip')}
              size="small"
              fullWidth
              disabled={!isEditable}
              sx={sharedSx}
              variant="outlined"
              label=""
            />
          </FormControl>


<FormControl fullWidth>
            <label style={{ fontSize: '0.78rem', marginBottom: '4px' }}>Contact</label>
            <TextField
              label=""
              value={selectedGroupRow.contact || ''}
              onChange={handleChange('contact')}
              size="small"
              fullWidth
              disabled={!isEditable}
              sx={sharedSx}
            />
          </FormControl>


          <FormControl fullWidth>
            <label style={{ fontSize: '0.78rem', marginBottom: '4px' }}>
              Billing Sys/Prin
            </label>
            <TextField
              label=""
              value={selectedGroupRow.billingSp || ''}
              onChange={handleChange('billingSp')}
              size="small"
              fullWidth
              disabled={!isEditable}
              sx={sharedSx}
            />
          </FormControl>
