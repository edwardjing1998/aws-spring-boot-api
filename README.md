<TextField
  select
  label=""
  value={selectedGroupRow.state || ''}
  onChange={(e) =>
    setSelectedGroupRow(prev => ({ ...(prev ?? {}), state: e.target.value }))
  }
  size="small"
  fullWidth
  disabled={!isEditable}
  sx={sharedSx}
  SelectProps={{
    MenuProps: {
      // ↓ smaller font just for the menu items
      sx: {
        '& .MuiMenuItem-root': {
          fontSize: '0.72rem',  // tweak to taste
          minHeight: 30,
          lineHeight: 1.2,
        },
      },
      PaperProps: { style: { maxHeight: 320 } },
    },
  }}
>
  {US_STATES.map((s) => (
    <MenuItem key={s.code} value={s.code}>
      {s.name} ({s.code})
    </MenuItem>
  ))}
</TextField>
