<CCol
  xs={2}
  style={{
    minWidth: 120,
    display: 'none',   // ✅ 完全隐藏
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
      value={String(xrefValue ?? '')}
      onChange={(e) => {
        const val = e.target.value;
        setSelectedGroupRow((prev) =>
          prev ? { ...prev, subClientXref: val, subClientXRef: val } : null
        );
      }}
      size="small"
      fullWidth
      disabled={!isEditable}
      sx={sharedSx}
    />
  </FormControl>
</CCol>
