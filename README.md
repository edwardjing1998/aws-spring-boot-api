      <CCard
        style={{
          marginTop: '30px',
          height: '80px',
          marginBottom: '4px',
          border: 'none',
          boxShadow: 'none',
          borderTop: '0px solid #ccc',
          borderRadius: '4px',
        }}
      >
        <CCardBody
          style={{
            padding: '0.25rem 0.5rem',
            height: '100%',
            backgroundColor: 'white',
            display: 'flex',
            flexDirection: 'column',
            justifyContent: 'space-between',
          }}
        >
          <CRow style={{ height: '25px' }}>
            <CCol style={{ display: 'flex', alignItems: 'center' }}>
              <p style={{ margin: 0, fontSize: '0.78rem' }}>Customer Type</p>
            </CCol>
            <CCol style={{ display: 'flex', alignItems: 'center' }}>
              <p style={{ margin: 0, fontSize: '0.78rem' }}>Return Status</p>
            </CCol>
            <CCol />
          </CRow>

          <CRow style={{ height: '25px' }}>
            <CCol style={{ display: 'flex', alignItems: 'center' }}>
              <span style={{ margin: 0, fontSize: '0.78rem' }}>{custTypeLabel}</span>
            </CCol>
            <CCol style={{ display: 'flex', alignItems: 'center' }}>
              <span style={{ margin: 0, fontSize: '0.78rem' }}>{returnStatusText}</span>
            </CCol>
            <CCol />
          </CRow>
        </CCardBody>
      </CCard>
