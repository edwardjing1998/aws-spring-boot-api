      <CCard style={{ height: '80px', marginBottom: '4px', marginTop: '15px', border: 'none', boxShadow: 'none', borderRadius: '4px' }}>
        <CCardBody style={{ padding: '0.25rem 0.5rem', height: '100%', backgroundColor: 'white', display: 'flex', flexDirection: 'column', justifyContent: 'space-between' }}>
          <CRow style={{ height: '25px' }}>
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

          <CRow style={{ height: '25px' }}>
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
