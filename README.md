<TextField
  value={form.emailBodyTx}
  onChange={(e) => setForm((p) => ({ ...p, emailBodyTx: e.target.value }))}
  size="small"
  fullWidth
  multiline
  rows={3}                          // base height
  inputProps={{ maxLength: 500 }}
  helperText={`${(form.emailBodyTx?.length ?? 0)}/500`}
  sx={{
    '& .MuiInputBase-inputMultiline': {
      maxHeight: 100,        // cap
      overflowY: 'auto',
      lineHeight: 1.35,
      padding: '6px 8px',
    },
  }}
/>
