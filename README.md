const CCODES = { '+1':'US', '+44':'UK', '+61':'AU', '+91':'IN', /* add more as needed */ };

<Select
  value={selectedGroupRow.phoneCountryCode || '+1'}
  onChange={handleChange('phoneCountryCode')}
  disabled={!isEditable}
  displayEmpty

  // ① no chevron icon
  IconComponent={() => null}

  // ② show simple text for the selected value
  renderValue={(val) => (val ? (CCODES[val] ?? String(val)) : '')}

  sx={{
    ...sharedSx,
    width: 70,

    '& .MuiOutlinedInput-root': {
      height: 24,
      minHeight: 24,
      position: 'relative',
    },

    '& .MuiOutlinedInput-input': {
      paddingTop: 0,
      paddingBottom: 0,
      paddingLeft: 8,
      paddingRight: 8,           // ← no icon, so tighten right padding
      fontSize: '0.78rem',
      lineHeight: 1.0,
    },

    '& .MuiSelect-select': {
      paddingTop: 0,
      paddingBottom: 0,
      minHeight: 0,
      fontSize: '0.78rem',
      lineHeight: 1.0,
    },

    // hard hide any theme-provided icon, just in case
    '& .MuiSelect-icon, & .MuiSelect-iconOutlined': { display: 'none' },
  }}

  MenuProps={{
    sx: {
      '& .MuiMenuItem-root': { fontSize: '0.72rem', minHeight: 28, lineHeight: 1.2 },
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
