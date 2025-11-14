      <CCard style={{ margin: '10px 0 0', minHeight: '20px', border: 'none', boxShadow: 'none', borderBottom: '1px solid #ccc' }}>
        <CCardBody style={{ padding: '0.25rem 0.5rem', backgroundColor: 'white', display: 'flex', flexDirection: 'column', rowGap: '2px' }}>
          <CRow style={{ height: '20px' }}>
            <CCol><p style={{ margin: 0, fontSize: '0.78rem' }}> {getStatusValue(isPOBox, selectedData?.poBox)}</p></CCol>
            <CCol><p style={{ margin: 0, fontSize: '0.78rem' }}> {getStatusValue(invalidState, selectedData?.badState)}</p></CCol>
          </CRow>
        </CCardBody>
      </CCard>
