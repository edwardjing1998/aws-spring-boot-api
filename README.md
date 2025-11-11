// Near top of the component/file (or merge into your existing MAX)
const MAX = { ...(MAX || {}), city: 18 };
const cityLen = (selectedGroupRow?.city ?? '').length;

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
