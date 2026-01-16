<TextField
  value={name}
  onChange={(e) => setName(e.target.value)}
  disabled={!isEditable}
  placeholder="Enter name"
  size="small"
  sx={{
    '& .MuiInputBase-root': {
      height: '30px',
      minHeight: '30px',
    },
    '& .MuiInputBase-input': {
      padding: '4px 6px',
      fontSize: '0.73rem',
      lineHeight: '22px',
    },
  }}
  inputProps={{
    maxLength: MAX.name,
    'aria-describedby': 'name-counter',
  }}
/>
