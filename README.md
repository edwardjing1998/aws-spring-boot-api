<TextField
  value={form.emailBodyTx}
  onChange={(e) => setForm((p) => ({ ...p, emailBodyTx: e.target.value }))}
  size="small"
  fullWidth
  multiline
  rows={8}                 // fixed height
  inputProps={{ maxLength: 500 }}
  helperText={`${(form.emailBodyTx?.length ?? 0)}/500`}
  sx={{ '& .MuiInputBase-inputMultiline': { overflowY: 'auto' } }}
/>
