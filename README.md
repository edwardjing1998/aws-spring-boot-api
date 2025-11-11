<Box sx={{ display: 'flex', alignItems: 'center', columnGap: 0.25 }}>
  <Box
    component="label"
    sx={{ fontSize: '0.78rem', minWidth: 60, position: 'relative', top: -4 }}
  >
    Name
  </Box>
  <TextField
    label=""
    value={viewRow.name ?? ''}
    size="small"
    disabled={!isEditable}
    sx={{ ...sharedSx, flex: 1, ml: 0 }}
    onChange={(e) =>
      setSelectedGroupRow(prev => ({ ...(prev ?? makeEmptyClient()), name: e.target.value }))
    }
    inputProps={{ maxLength: MAX.name }}
    helperText={`${viewRow.name.length}/${MAX.name}`}
  />
</Box>
