{/* Address row – allow wrapping */}
<CRow style={{ minHeight: '20px' }}>
  <CCol
    style={{
      flex: '0 0 100%',
      fontSize: '0.78rem',
      textAlign: 'left',
      whiteSpace: 'normal',     // allow wrapping
      wordBreak: 'break-word',  // break long words if needed
      lineHeight: '1.1rem',
    }}
  >
    {selectedGroupRow?.addr || 'xxxxx'}
  </CCol>
</CRow>

{/* City / State row – can be fixed height or also minHeight if you want wrapping */}
<CRow style={{ height: '20px' }}>
  <CCol
    style={{
      flex: '0 0 100%',
      fontSize: '0.78rem',
      textAlign: 'left',
      whiteSpace: 'nowrap',     // keep on one line (optional)
      overflow: 'hidden',
      textOverflow: 'ellipsis',
    }}
  >
    {selectedGroupRow?.city || 'xxxxx'}, {selectedGroupRow?.state || 'xx'}
  </CCol>
</CRow>

{/* Zip row with bottom border – stays properly aligned */}
<CRow style={{ height: '20px', borderBottom: '1px solid #ccc' }}>
  <CCol
    style={{
      flex: '0 0 100%',
      fontSize: '0.78rem',
      textAlign: 'left',
    }}
  >
    {selectedGroupRow?.zip || 'xxxxx'}
  </CCol>
</CRow>
