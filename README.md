// somewhere near the top of your file
const MAX = { addr: 35 }; // use lower-case to match your state key
const addrLen = (selectedGroupRow?.addr ?? "").length;

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
