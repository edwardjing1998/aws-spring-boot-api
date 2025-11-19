                <div style={{ minHeight: 64 }}>
                  <CFormSelect
                    label="Email Server"
                    value={emailServer}
                    onChange={(e) => setEmailServer(e.target.value)}
                    disabled={!isEditable}
                    style={{ fontSize: '0.78rem', width: 260 }}
                  >
                    <option value="">Select Email Server</option>
                    {emailServers.map((srv, idx) => (
                      <option key={idx} value={srv}>
                        {srv}
                      </option>
                    ))}
                  </CFormSelect>
                </div>
