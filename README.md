{/* ===== Left card: selects + checkboxes in two rows (4 cols each) ===== */}
<CCol xs={6}>
  <CCard className="mb-4">
    <CCardBody>

      {/* Row 1 — 4 columns of selects */}
      <CRow className="mb-3">
        {/* Customer Type */}
        <CCol xs={3}>
          <div style={{ ...rowStyle, gap: '12px', height: 'auto' }}>
            <div id="customer-type-inline-label" style={leftLabel}>Customer Type</div>
            <FormControl fullWidth size="small" disabled={!isEditable} sx={{ flex: 1 }}>
              <Select
                id="customer-type"
                aria-labelledby="customer-type-inline-label"
                value={custType}
                onChange={handleChange('custType')}
                sx={{ fontSize: '0.78rem' }}
              >
                <MenuItem value="0" sx={{ fontSize: '0.78rem' }}><em>None</em></MenuItem>
                <MenuItem value="1" sx={{ fontSize: '0.78rem' }}>Full Processing</MenuItem>
                <MenuItem value="2" sx={{ fontSize: '0.78rem' }}>Destroy All</MenuItem>
                <MenuItem value="3" sx={{ fontSize: '0.78rem' }}>Return All</MenuItem>
              </Select>
            </FormControl>
          </div>
        </CCol>

        {/* Return Status */}
        <CCol xs={3}>
          <div style={{ ...rowStyle, gap: '12px', height: 'auto' }}>
            <div id="return-status-inline-label" style={leftLabel}>Return Status</div>
            <FormControl fullWidth size="small" disabled={!isEditable} sx={{ flex: 1 }}>
              <Select
                id="return-status"
                aria-labelledby="return-status-inline-label"
                value={returnStatus}
                onChange={handleChange('returnStatus')}
                sx={{ fontSize: '0.78rem' }}
              >
                <MenuItem value=""  sx={{ fontSize: '0.78rem' }}>None</MenuItem>
                <MenuItem value="A" sx={{ fontSize: '0.78rem' }}>A Status</MenuItem>
                <MenuItem value="C" sx={{ fontSize: '0.78rem' }}>C Status</MenuItem>
                <MenuItem value="E" sx={{ fontSize: '0.78rem' }}>E Status</MenuItem>
                <MenuItem value="F" sx={{ fontSize: '0.78rem' }}>F Status</MenuItem>
              </Select>
            </FormControl>
          </div>
        </CCol>

        {/* Destroy Status */}
        <CCol xs={3}>
          <div style={{ ...rowStyle, gap: '12px', height: 'auto' }}>
            <div id="destroy-status-inline-label" style={leftLabel}>Destroy Status</div>
            <FormControl fullWidth size="small" disabled={!isEditable} sx={{ flex: 1 }}>
              <Select
                id="destroy-status"
                aria-labelledby="destroy-status-inline-label"
                value={destroyStatus}
                onChange={handleChange('destroyStatus')}
                sx={{ fontSize: '0.78rem' }}
              >
                <MenuItem value="0" sx={{ fontSize: '0.78rem' }}>None</MenuItem>
                <MenuItem value="1" sx={{ fontSize: '0.78rem' }}>Destroy</MenuItem>
                <MenuItem value="2" sx={{ fontSize: '0.78rem' }}>Return</MenuItem>
              </Select>
            </FormControl>
          </div>
        </CCol>

        {/* Special */}
        <CCol xs={3}>
          <div style={{ ...rowStyle, gap: '12px', height: 'auto' }}>
            <div id="special-inline-label" style={leftLabel}>Special</div>
            <FormControl fullWidth size="small" disabled={!isEditable} sx={{ flex: 1 }}>
              <Select
                id="special-option"
                aria-labelledby="special-inline-label"
                value={special}
                onChange={handleChange('special')}
                sx={{ fontSize: '0.78rem' }}
              >
                <MenuItem value="0" sx={{ fontSize: '0.78rem' }}>None</MenuItem>
                <MenuItem value="1" sx={{ fontSize: '0.78rem' }}>Destroy</MenuItem>
                <MenuItem value="2" sx={{ fontSize: '0.78rem' }}>Return</MenuItem>
              </Select>
            </FormControl>
          </div>
        </CCol>
      </CRow>

      {/* Row 2 — 4 columns: Pin Mailer + 2 short checkboxes + stacked long checkboxes */}
      <CRow>
        {/* Pin Mailer */}
        <CCol xs={3} className="mb-3">
          <div style={{ ...rowStyle, gap: '12px', height: 'auto' }}>
            <div id="pin-mailer-inline-label" style={leftLabel}>Pin Mailer</div>
            <FormControl fullWidth size="small" disabled={!isEditable} sx={{ flex: 1 }}>
              <Select
                id="pin-mailer"
                aria-labelledby="pin-mailer-inline-label"
                value={pinMailer}
                onChange={handleChange('pinMailer')}
                sx={{ fontSize: '0.78rem' }}
              >
                <MenuItem value="0" sx={{ fontSize: '0.78rem' }}>Non</MenuItem>
                <MenuItem value="1" sx={{ fontSize: '0.78rem' }}>Destroy</MenuItem>
                <MenuItem value="2" sx={{ fontSize: '0.78rem' }}>Return</MenuItem>
              </Select>
            </FormControl>
          </div>
        </CCol>

        {/* RPS Customer */}
        <CCol xs={3} className="mb-3">
          <div style={rowStyle}>
            <CFormCheck
              type="checkbox"
              id="rps-customer"
              label={<span style={labelStyle}>RPS Customer</span>}
              checked={rps === 'Y'}
              onChange={handleCheckboxChange('rps')}
              disabled={!isEditable}
            />
          </div>
        </CCol>

        {/* Sys/PRIN Active */}
        <CCol xs={3} className="mb-3">
          <div style={{ ...rowStyle, height: 'auto' }}>
            <CFormCheck
              type="checkbox"
              id="sys-prin-active"
              label={<span style={labelStyle}>Sys/PRIN Active</span>}
              checked={sysPrinActive === 'Y'}
              onChange={handleCheckboxChange('sysPrinActive')}
              disabled={!isEditable}
            />
          </div>
        </CCol>

        {/* Stacked long-label checks */}
        <CCol xs={3}>
          <div style={{ display: 'flex', flexDirection: 'column', gap: 8 }}>
            <CFormCheck
              type="checkbox"
              id="flag-undeliverable"
              label={<span style={labelStyle}>Flag Undeliverable an Invalid Address</span>}
              checked={addrFlag === '1'}
              onChange={handleCheckboxChange('addrFlag')}
              disabled={!isEditable}
            />
            <CFormCheck
              type="checkbox"
              id="status-research"
              label={<span style={labelStyle}>A Status Accounts Going in Research</span>}
              checked={astatRch === '1'}
              onChange={handleCheckboxChange('astatRch')}
              disabled={!isEditable}
            />
            <CFormCheck
              type="checkbox"
              id="perform-non-mon"
              label={<span style={labelStyle}>Perform Non Mon 13 on Destroy</span>}
              checked={nm13 === '1'}
              onChange={handleCheckboxChange('nm13')}
              disabled={!isEditable}
            />
          </div>
        </CCol>
      </CRow>

      {/* Update button aligned right */}
      <div style={{ display: 'flex', justifyContent: 'flex-end', marginTop: 16 }}>
        <Button
          variant="contained"
          size="small"
          onClick={handleUpdate}
          disabled={updating || !isEditable || !selectedData?.client || !selectedData?.sysPrin}
        >
          {updating ? 'Updating…' : 'Update'}
        </Button>
      </div>

    </CCardBody>
  </CCard>
</CCol>
