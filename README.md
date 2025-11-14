<CCard
  style={{
    // height: '80px',          // ⬅️ remove fixed height
    marginBottom: '4px',
    marginTop: '15px',
    border: 'none',
    boxShadow: 'none',
    borderRadius: '4px',
  }}
>
  <CCardBody
    style={{
      padding: '0.25rem 0.5rem',
      // height: '100%',        // optional to remove
      backgroundColor: 'white',
      display: 'flex',
      flexDirection: 'column',
      justifyContent: 'flex-start',  // ⬅️ was 'space-between'
      rowGap: '2px',                 // ⬅️ small vertical gap between rows
    }}
  >
    <CRow style={{ height: '20px' }}>
      <CCol style={{ display: 'flex', alignItems: 'center' }}>
        <p style={{ margin: 0, fontSize: '0.78rem' }}>Special</p>
      </CCol>
      <CCol style={{ display: 'flex', alignItems: 'center' }}>
        <p style={{ margin: 0, fontSize: '0.78rem' }}>Pin Mailer</p>
      </CCol>
      <CCol style={{ display: 'flex', alignItems: 'center' }}>
        <p style={{ margin: 0, fontSize: '0.78rem' }}>Destroy Status</p>
      </CCol>
    </CRow>

    <CRow style={{ height: '20px' }}>
      <CCol style={{ display: 'flex', alignItems: 'center' }}>
        <span style={{ margin: 0, fontSize: '0.78rem' }}>{specialLabel}</span>
      </CCol>

      <CCol style={{ display: 'flex', alignItems: 'center' }}>
        <span style={{ margin: 0, fontSize: '0.78rem' }}>{pinMailerLabel}</span>
      </CCol>

      <CCol style={{ display: 'flex', alignItems: 'center' }}>
        <span style={{ margin: 0, fontSize: '0.78rem' }}>{destroyLabel}</span>
      </CCol>
    </CRow>
  </CCardBody>
</CCard>
