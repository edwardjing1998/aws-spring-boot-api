<TextField
  select
  label=""
  value={selectedGroupRow.state || ''}   // 👈 '' will map to "Select"
  onChange={(e) =>
    setSelectedGroupRow(prev => ({ ...(prev ?? {}), state: e.target.value }))
  }
  size="small"
  fullWidth
  disabled={!isEditable}
  sx={sharedSx}
  SelectProps={{
    MenuProps: {
      sx: {
        '& .MuiMenuItem-root': {
          fontSize: '0.72rem',
          minHeight: 30,
          lineHeight: 1.2,
        },
      },
      PaperProps: { style: { maxHeight: 320 } },
    },
  }}
>
  {US_STATES.map((s) => (
    <MenuItem key={s.code || 'select'} value={s.code}>
      {s.name}
      {s.code && ` (${s.code})`}
    </MenuItem>
  ))}
</TextField>
