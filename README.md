        <CCol xs={3}>
          <div style={{ ...rowStyle, gap: '12px', height: 'auto' }}>
            <div id="destroy-status-inline-label" style={leftLabel}>Destroy Status</div>
            <FormControl fullWidth size="small" disabled={!isEditable} sx={{ flex: 1 }}>
              <Select
                id="destroy-status"
                aria-labelledby="destroy-status-inline-label"
                value={destroyStatus}
                onChange={handleChange('destroyStatus')}
                sx={{ fontSize: '0.78rem' }}
              >
                <MenuItem value="0" sx={{ fontSize: '0.78rem' }}>None</MenuItem>
                <MenuItem value="1" sx={{ fontSize: '0.78rem' }}>Destroy</MenuItem>
                <MenuItem value="2" sx={{ fontSize: '0.78rem' }}>Return</MenuItem>
              </Select>
            </FormControl>
          </div>
        </CCol>
