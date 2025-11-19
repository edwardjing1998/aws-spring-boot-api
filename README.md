<div
  style={{
    minHeight: 32,
    display: 'flex',
    alignItems: 'center',
    gap: 8,              // space between label and select
  }}
>
  <span
    style={{
      fontSize: '0.78rem',
      whiteSpace: 'nowrap',
    }}
  >
    Email Server
  </span>

  <CFormSelect
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

