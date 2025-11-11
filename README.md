// Merge into your constants
const MAX = { ...(MAX ?? {}), zip: 9, contact: 20, billingSp: 8 };

const zipLen      = (selectedGroupRow?.zip ?? '').length;
const contactLen  = (selectedGroupRow?.contact ?? '').length;
const billingLen  = (selectedGroupRow?.billingSp ?? '').length;

// --- Zip Code ---
<FormControl fullWidth>
  <label
    htmlFor="zip-input"
    style={{ fontSize: '0.78rem', marginBottom: '4px', display: 'flex', alignItems: 'center', gap: 6 }}
  >
    Zip Code
    <span
      id="zip-counter"
      style={{ fontSize: '0.72rem', color: zipLen >= MAX.zip ? '#d32f2f' : 'gray' }}
    >
      ({zipLen}/{MAX.zip})
    </span>
  </label>

  <TextField
    id="zip-input"
    value={selectedGroupRow.zip || ''}
    onChange={handleChange('zip')}
    size="small"
    fullWidth
    disabled={!isEditable}
    sx={sharedSx}
    variant="outlined"
    label=""
    inputProps={{
      maxLength: MAX.zip,
      'aria-describedby': 'zip-counter',
      inputMode: 'numeric', // optional UX hint on mobile keyboards
    }}
  />
</FormControl>

// --- Contact ---
<FormControl fullWidth>
  <label
    htmlFor="contact-input"
    style={{ fontSize: '0.78rem', marginBottom: '4px', display: 'flex', alignItems: 'center', gap: 6 }}
  >
    Contact
    <span
      id="contact-counter"
      style={{ fontSize: '0.72rem', color: contactLen >= MAX.contact ? '#d32f2f' : 'gray' }}
    >
      ({contactLen}/{MAX.contact})
    </span>
  </label>

  <TextField
    id="contact-input"
    label=""
    value={selectedGroupRow.contact || ''}
    onChange={handleChange('contact')}
    size="small"
    fullWidth
    disabled={!isEditable}
    sx={sharedSx}
    inputProps={{
      maxLength: MAX.contact,
      'aria-describedby': 'contact-counter',
    }}
  />
</FormControl>

// --- Billing Sys/Prin ---
<FormControl fullWidth>
  <label
    htmlFor="billingsp-input"
    style={{ fontSize: '0.78rem', marginBottom: '4px', display: 'flex', alignItems: 'center', gap: 6 }}
  >
    Billing Sys/Prin
    <span
      id="billingsp-counter"
      style={{ fontSize: '0.72rem', color: billingLen >= MAX.billingSp ? '#d32f2f' : 'gray' }}
    >
      ({billingLen}/{MAX.billingSp})
    </span>
  </label>

  <TextField
    id="billingsp-input"
    label=""
    value={selectedGroupRow.billingSp || ''}
    onChange={handleChange('billingSp')}
    size="small"
    fullWidth
    disabled={!isEditable}
    sx={sharedSx}
    inputProps={{
      maxLength: MAX.billingSp,
      'aria-describedby': 'billingsp-counter',
      inputMode: 'numeric', // optional if it’s digits only
    }}
  />
</FormControl>
