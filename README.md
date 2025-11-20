<CRow className="mt-2" style={{ marginTop: 8 }}>
              <CCol
                className="d-flex justify-content-center"
                style={{ gap: 8, paddingTop: 2, paddingBottom: 2 }}
              >
                <Button
                  variant="outlined"
                  size="small"
                  onClick={handleUpdateEmail}
                  sx={{ fontSize: '0.73rem', textTransform: 'none' }}
                >
                  Update
                </Button>
                <Button
                  variant="outlined"
                  size="small"
                  onClick={handleAddEmail}
                  sx={{ fontSize: '0.73rem', textTransform: 'none' }}
                  color="primary"
                >
                  Add
                </Button>
                <Button
                  variant="outlined"
                  size="small"
                  onClick={handleRemoveEmail}
                  sx={{ fontSize: '0.73rem', textTransform: 'none' }}
                  color="error"
                >
                  Remove
                </Button>
              </CCol>
            </CRow>
