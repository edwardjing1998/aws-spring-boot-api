<CRow style={{ fontSize: '0.73rem', maxHeight: 420, overflowY: 'auto', marginBottom: 0 }}>
      <CCol xs={12}>
        <CCard>
          <CCardBody style={{ padding: 6 }}>
            <div
              className="mb-3"
              style={{
                fontSize: '0.73rem',
                display: 'grid',
                gridTemplateColumns: 'auto auto',
                columnGap: '16px',
                alignItems: 'start',
              }}
            >
              <div style={{ minHeight: 70, borderBottom: '1px dotted #ccc' }}>
                <div style={{ marginBottom: 6 }}>Name:</div>
                <CFormInput
                  value={name}
                  onChange={(e) => setName(e.target.value)}
                  disabled={!isEditable}
                  placeholder="Enter name"
                  style={{ fontSize: '0.73rem', width: 260, marginLeft: 0 }}
                />
              </div>

              <div style={{ minHeight: 70, borderBottom: '1px dotted #ccc', marginTop: 10 }}>
                <div style={{ marginBottom: 6 }}>Email Address:</div>
                <CFormInput
                  value={emailAddress}
                  onChange={(e) => setEmailAddress(e.target.value)}
                  placeholder="Enter email address"
                  disabled={!isEditable}
                  style={{ fontSize: '0.73rem', width: 260 }}
                  type="email"
                />
              </div>

              <div style={{ gridRow: '1 / span 5' }}>
                <div style={{ marginLeft: 8 }}>
                  <div style={{ marginBottom: 12 }}>Email Recipients:</div>
                  <CFormSelect
                    multiple
                    value={selectedRecipients}
                    onChange={(e) => handleChange(e.target.selectedOptions)}
                    disabled={!isEditable}
                    style={{
                      fontSize: '0.73rem',
                      height: 220,
                      maxWidth: 400,
                      width: 400,
                      marginLeft: 0,
                    }}
                  >
                    {options.map((email, idx) => (
                      <option key={idx} value={email} style={{ fontSize: '0.73rem' }}>
                        {email}
                      </option>
                    ))}
                  </CFormSelect>
                </div>
              </div>

              <div style={{ minHeight: 70, marginTop: 10, borderBottom: '1px dotted #ccc' }}>
                <div style={{ marginBottom: 6 }}>Email Server:</div>
                <CFormSelect
                  value={emailServer}
                  onChange={(e) => setEmailServer(e.target.value)}
                  disabled={!isEditable}
                  style={{ fontSize: '0.73rem', width: 260 }}
                >
                  <option value="">Select Email Server</option>
                  {emailServers.map((srv, idx) => (
                    <option key={idx} value={srv}>
                      {srv}
                    </option>
                  ))}
                </CFormSelect>
              </div>

              <div
                style={{
                  minHeight: 64,
                  display: 'flex',
                  alignItems: 'center',
                  gap: '24px',
                  marginTop: -5,
                }}
              >
                <CFormCheck
                  label="Active"
                  checked={isActive}
                  onChange={(e) => setIsActive(e.target.checked)}
                  disabled={!isEditable}
                  style={{ fontSize: '0.73rem' }}
                />
                <CFormCheck
                  label="CC"
                  checked={isCC}
                  onChange={(e) => setIsCC(e.target.checked)}
                  disabled={!isEditable}
                  style={{ fontSize: '0.73rem' }}
                />

                <div
                  style={{
                    display: 'flex',
                    alignItems: 'center',
                    gap: 10,
                    marginTop: -10,
                  }}
                >
                  <span
                    style={{
                      fontSize: '0.73rem',
                      marginRight: 2,
                    }}
                  >
                    Report ID
                  </span>

                  <CFormInput
                    value={reportId}
                    onChange={(e) => setReportId(e.target.value)}
                    placeholder="Enter report id"
                    disabled={!isEditable}
                    size="sm"
                    type="number"
                    inputMode="numeric"
                    style={{
                      fontSize: '0.73rem',
                      width: 60,
                      height: '24px',
                      padding: '0 4px',
                      lineHeight: '24px',
                      margin: 0,
                    }}
                  />
                </div>
              </div>
            </div>

            <CRow style={{ fontSize: '0.73rem', marginBottom: 8 }}>
              <CCol>
                <div
                  style={{
                    height: 22,
                    lineHeight: '22px',
                    overflow: 'hidden',
                    whiteSpace: 'nowrap',
                    textOverflow: 'ellipsis',
                    fontWeight: 'bold',
                    visibility: statusMessage ? 'visible' : 'hidden',
                    color: statusMessage?.toLowerCase().includes('error')
                      ? '#d32f2f'
                      : '#2e7d32',
                  }}
                  aria-live="polite"
                >
                  {statusMessage || ''}
                </div>
              </CCol>
            </CRow>

            <CRow className="mt-2" style={{ marginTop: 8 }}>
              <CCol
                // remove justify-content-center and control with inline style
                className="d-flex"
                style={{
                  gap: 8,
                  paddingTop: 2,
                  paddingBottom: 2,
                  justifyContent: 'flex-end',   // 👈 push buttons to the right
                }}
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
          </CCardBody>
        </CCard>
      </CCol>
    </CRow>
