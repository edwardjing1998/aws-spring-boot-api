<div
                style={{
                  minHeight: 55,
                  display: 'flex',
                  alignItems: 'center',
                  gap: '24px',
                  marginTop: 0,
                  borderBottom: '1px dotted #ccc'
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
                    ID
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
