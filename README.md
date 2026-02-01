<CCol
  xs={2}
  style={{
    minWidth: 120,
    visibility: 'hidden',     // hides it but keeps the space (layout unchanged)
    pointerEvents: 'none',    // prevents clicking/tabbing
  }}
>
  <FormControl fullWidth>
    <label
      htmlFor="subClientXref-input"
      style={{
        fontSize: '0.78rem',
        marginBottom: '4px',
        display: 'flex',
        alignItems: 'center',
        gap: 6,
      }}
    >
      XRef
      <span
        id="subClientXref-counter"
        style={{
          fontSize: '0.72rem',
          color: subClientXrefLen >= MAX.subClientXref ? '#d32f2f' : 'gray',
        }}
      >
        ({subClientXrefLen}/{MAX.subClientXref})
      </span>
    </label>

    <TextField
      id="subClientXref-input"
      label=""
      value={selectedGroupRow?.subClientXref || ''}
      onChange={handleChange('subClientXref')}
      size="small"
      fullWidth
      disabled={!isEditable}
      sx={sharedSx}
      inputProps={{
        maxLength: MAX.subClientXref,
        'aria-describedby': 'subClientXref-counter',
        inputMode: 'numeric',
      }}
    />
  </FormControl>
</CCol>
