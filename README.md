{/* Change All controls */}
{mode === 'changeAll' && (
  <Box sx={{ mt: 2, display: 'grid', gridTemplateColumns: '1fr 1fr', gap: 2 }}>
    <TextField
      size="small"
      label="Client ID (defaults to selected client)"
      value={oldClientId}
      onChange={(e) => setOldClientId(e.target.value)}
      InputProps={{ sx: { fontSize: '0.85rem' } }}
      helperText="Leave blank to use the right-pane selected client"
    />
    <TextField
      size="small"
      label="(Optional) New Client ID"
      value={newClientId}
      onChange={(e) => setNewClientId(e.target.value)}
      InputProps={{ sx: { fontSize: '0.85rem' } }}
      helperText="For future cross-client copy; not used by this API"
    />
  </Box>
)}
