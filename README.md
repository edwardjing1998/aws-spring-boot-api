const primaryLabel =
  mode === 'changeAll' ? 'Change All'
  : mode === 'duplicate' ? 'Duplicate'
  : mode === 'move' ? 'Move'
  : mode === 'edit' ? 'Update'
  : mode === 'new' ? 'Create'
  : 'Create';



<CCol style={{ display: 'flex', justifyContent: 'flex-end', gap: '12px' }}>
  <Button
    variant="contained"
    size="small"
    onClick={handlePrimaryClick}
    disabled={saving}
  >
    {primaryLabel}
  </Button>

  <Button
    variant="outlined"
    size="small"
    onClick={() => setTabIndex((i) => Math.min(i + 1, 6))}
  >
    Next
  </Button>
</CCol>


