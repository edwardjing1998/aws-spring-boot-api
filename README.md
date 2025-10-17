<Box gridColumn="1 / span 2">
  <Typography sx={labelSx}>Email Body</Typography>
  {isEditable ? (
    <TextField
      value={form.emailBodyTx}
      onChange={(e) => setForm((p) => ({ ...p, emailBodyTx: e.target.value }))}
      size="small"
      fullWidth
      multiline
      minRows={3}
      maxRows={8}                 // grow up to 8 rows, then scroll
      inputProps={{ maxLength: 500 }} // hard cap at 500 chars
      helperText={`${(form.emailBodyTx?.length ?? 0)}/500`}
      sx={{
        '& textarea': {
          maxHeight: 160,         // ~8 rows; adjust as you like
          overflowY: 'auto',
        },
      }}
    />
  ) : (
    <Box
      sx={{
        fontSize: '0.9rem',
        whiteSpace: 'pre-wrap',
        wordBreak: 'break-word',
        maxHeight: 160,           // same visual cap as edit mode
        overflowY: 'auto',
        border: '1px solid rgba(0,0,0,0.23)',
        borderRadius: 1,
        p: 1,
        bgcolor: '#fafafa',
      }}
    >
      {form.emailBodyTx || '(empty)'}
    </Box>
  )}
</Box>
