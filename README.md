          <FormControl fullWidth>
            <label style={{ fontSize: '0.78rem', marginBottom: '4px' }}>City</label>
            <TextField
              label=""
              value={selectedGroupRow.city || ''}
              onChange={handleChange('city')}
              size="small"
              fullWidth
              disabled={!isEditable}
              sx={sharedSx}
            />
          </FormControl>
