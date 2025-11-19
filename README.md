<CFormInput
  label="report ID"
  value={reportId}
  onChange={(e) => setReportId(e.target.value)}
  placeholder="Enter report id"
  disabled={!isEditable}
  size="sm"                      // ↓ built-in smaller height
  type="number"
  inputMode="numeric"
  style={{
    fontSize: '0.73rem',
    width: 60,
    height: '24px',              // ↓ explicit height
    padding: '0 4px',            // ↓ less vertical padding
    lineHeight: '24px',          // matches height to center text
  }}
/>
