          <FormControl fullWidth>
            <label style={{ fontSize: '0.78rem', marginBottom: '4px' }}>State</label>
            <TextField
              label=""
              value={selectedGroupRow.state || ''}
              onChange={handleChange('state')}
              size="small"
              fullWidth
              disabled={!isEditable}
              sx={sharedSx}
            />
          </FormControl>
