<div
  style={{
    display: 'flex',
    alignItems: 'center',
    gap: 4,                 // ↓ controls space between label and input
  }}
>
  <span
    style={{
      fontSize: '0.73rem',
      marginRight: 2,       // extra fine-tuning if you like
    }}
  >
    Report ID:
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
      margin: 0,           // make sure no extra top margin
    }}
  />
</div>
