<CRow style={{ height: '35px' }}>
  <CCol>
    <p style={{ margin: 0, fontSize: '0.78rem' }}>Billing Sys/Prin</p>
  </CCol>
  <CCol>
    <p style={{ margin: 0, fontSize: '0.78rem' }}>Report Breaks</p>
  </CCol>
  <CCol>
    <p style={{ margin: 0, fontSize: '0.78rem' }}>Search Type</p>
  </CCol>

  {/* Address column, right-aligned */}
  <CCol
    style={{
      textAlign: 'right',          // right-align text
      display: 'flex',
      flexDirection: 'column',
      justifyContent: 'center',
    }}
  >
    <CRow style={{ height: '20px' }}>
      <CCol style={{ flex: '0 0 100%', fontSize: '0.78rem', textAlign: 'right' }}>
        2314 S 122nd Ave
      </CCol>
    </CRow>
    <CRow style={{ height: '20px' }}>
      <CCol style={{ flex: '0 0 100%', fontSize: '0.78rem', textAlign: 'right' }}>
        Omaha, NE
      </CCol>
    </CRow>
    <CRow style={{ height: '20px' }}>
      <CCol style={{ flex: '0 0 100%', fontSize: '0.78rem', textAlign: 'right' }}>
        68144
      </CCol>
    </CRow>
  </CCol>
</CRow>
