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
      {/* Default / None option */}
      <MenuItem value="" sx={{ fontSize: '0.78rem' }}>
        None
      </MenuItem>

      {/* Options from SEARCH_TYPE_OPTIONS */}
      {SEARCH_TYPE_OPTIONS.map((opt) => (
        <MenuItem
          key={opt.value}
          value={opt.value}
          sx={{ fontSize: '0.78rem' }}
        >
          {opt.label}
        </MenuItem>
      ))}
    </Select>
  </FormControl>
</CCol>
