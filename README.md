const fieldSx = {
  '& .MuiInputBase-root': {
    height: 32,            // same height for all
    fontSize: '0.85rem',
  },
  '& .MuiInputBase-input': {
    padding: '4px 8px',
    fontSize: '0.85rem',
  },
  '& .MuiInputLabel-root': {
    fontSize: '0.78rem',
  },
};

<Box
  sx={{
    mt: 2,
    display: 'grid',
    gridTemplateColumns: 'repeat(3, 1fr)', // 3 equal columns
    gap: 2,
    alignItems: 'center',
  }}
>
  <TextField
    fullWidth
    size="small"
    label="Sys/Prin ID"
    placeholder="Enter Sys/Prin ID"
    value={sysPrinInput}
    onChange={(e) => {
      setSysPrinInput(e.target.value);   // smooth typing
    }}
    onBlur={() => {
      // when user leaves the field, sync to selectedData once
      setSelectedData((prev) => ({
        ...(prev ?? {}),
        sysPrin: sysPrinInput,
      }));
    }}
    disabled={!isEditable && (mode === 'changeAll' || mode === 'delete')}
    sx={fieldSx}
  />

  <TextField
    fullWidth
    size="small"
    label="Old Client ID"
    value={oldClientId}
    onChange={(e) => setOldClientId(e.target.value)}
    sx={fieldSx}
  />

  <TextField
    fullWidth
    size="small"
    label="New Client ID"
    value={newClientId}
    onChange={(e) => setNewClientId(e.target.value)}
    sx={fieldSx}
  />
</Box>
