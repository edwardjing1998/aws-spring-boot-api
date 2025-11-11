// put near the component top
const COUNTRIES = [
  { value: 'US', label: 'US', dial: '+1',  flag: 'US' },
  { value: 'CA', label: 'CA', dial: '+1',  flag: 'CA' },
  { value: 'GB', label: 'UK', dial: '+44', flag: 'GB' },
  { value: 'AU', label: 'AU', dial: '+61', flag: 'AU' },
  { value: 'IN', label: 'IN', dial: '+91', flag: 'IN' },
];

// helpers
const getCountry = (val) => COUNTRIES.find(o => o.value === val) || COUNTRIES[0];



<FormControl sx={{ minWidth: 90 }} size="small">
  <Select
    value={selectedGroupRow.phoneCountry || 'US'}     // ← store the country key
    onChange={(e) => {
      const c = getCountry(e.target.value);
      // keep your original phoneCountryCode in sync with the dial value
      handleChange('phoneCountryCode')({ target: { value: c.dial }});
      // also store the country key (add this field to your row if not present)
      handleChange('phoneCountry')({ target: { value: c.value }});
    }}
    disabled={!isEditable}
    displayEmpty
    IconComponent={() => null} // hide chevron
    renderValue={(val) => getCountry(val).label}
    sx={{
      ...sharedSx,
      width: 70,

      '& .MuiOutlinedInput-root': {
        height: 24,
        minHeight: 24,
        position: 'relative',
      },

      // center the text vertically
      '& .MuiOutlinedInput-input': {
        paddingTop: 0,
        paddingBottom: 0,
        paddingLeft: 8,
        paddingRight: 8,
        fontSize: '0.78rem',
        height: 24,
        lineHeight: '24px',
        display: 'flex',
        alignItems: 'center',
      },
      '& .MuiSelect-select': {
        paddingTop: 0,
        paddingBottom: 0,
        minHeight: 0,
        fontSize: '0.78rem',
        height: 24,
        lineHeight: '24px',
        display: 'flex',
        alignItems: 'center',
      },

      // just in case a theme still injects it
      '& .MuiSelect-icon, & .MuiSelect-iconOutlined': { display: 'none' },
    }}
    MenuProps={{
      sx: {
        '& .MuiMenuItem-root': { fontSize: '0.72rem', minHeight: 26, lineHeight: 1.2 },
      },
      PaperProps: { style: { maxHeight: 260 } },
    }}
  >
    {COUNTRIES.map(c => (
      <MenuItem key={c.value} value={c.value}>
        <ReactCountryFlag
          countryCode={c.flag}
          svg
          style={{ width: '1em', height: '1em', marginRight: 6 }}
        />
        {c.label} <span style={{ marginLeft: 6, opacity: 0.7 }}>{c.dial}</span>
      </MenuItem>
    ))}
  </Select>
</FormControl>
