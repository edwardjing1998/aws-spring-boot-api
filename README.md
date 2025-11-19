                <CRow style={{ height: '20px' }}>
                  <CCol style={{ flex: '0 0 100%', fontSize: '0.78rem', textAlign: 'left' }}>
                     {selectedGroupRow?.addr || 'xxxxx'}
                  </CCol>
                </CRow>
                <CRow style={{ height: '20px' }}>
                  <CCol style={{ flex: '0 0 100%', fontSize: '0.78rem', textAlign: 'left' }}>
                    {selectedGroupRow?.city || 'xxxxx'}, {selectedGroupRow?.state || 'xx'}
                  </CCol>
                </CRow>
                <CRow style={{ height: '20px', borderBottom: '1px solid #ccc' }}>
                  <CCol style={{ flex: '0 0 100%', fontSize: '0.78rem', textAlign: 'left' }}>
                    {selectedGroupRow?.zip || 'xxxxx'}
                  </CCol>
                </CRow>
