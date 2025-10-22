import { 
  CCard, 
  CCardBody, 
  CCol, 
  CRow, 
  CFormCheck 
} from '@coreui/react';

import Select from '@mui/material/Select';
import MenuItem from '@mui/material/MenuItem';
import FormControl from '@mui/material/FormControl';

import '../../../../scss/sys-prin-configuration/client-atm-pin-prefixes.scss';

const rowStyle = {
  display: 'flex',
  alignItems: 'center',
  height: '50px',
};

const labelStyle = { margin: 0, fontSize: '0.78rem' };

const SysPrinGeneral = ({ selectedData, setSelectedData, isEditable }) => {
  const custType = selectedData?.custType || '';
  const returnStatus = selectedData?.returnStatus || '';
  const destroyStatus = selectedData?.destroyStatus || '';
  const special = selectedData?.special || '';
  const pinMailer = selectedData?.pinMailer || '';
  const active = selectedData?.active === true || selectedData?.active === 'Y' ? 'Y' : 'N';
  const rps = selectedData?.rps === true || selectedData?.rps === 'Y' ? 'Y' : 'N';
  const addrFlag = selectedData?.addrFlag === true || selectedData?.addrFlag === 'Y' ? 'Y' : 'N';
  const astatRch = selectedData?.astatRch === true || selectedData?.astatRch === '1' ? '1' : '0';
  const nm13 = selectedData?.nm13 === true || selectedData?.nm13 === '1' ? '1' : '0';

  const handleChange = (field) => (e) => {
    const value = e.target.value;
    setSelectedData((prev) => ({ ...prev, [field]: value }));
  };

  const handleCheckboxChange = (field) => (e) => {
    const checked = e.target.checked;
    let value = '';
    if (['active', 'rps', 'addrFlag'].includes(field)) {
      value = checked ? 'Y' : 'N';
    } else if (['astatRch', 'nm13'].includes(field)) {
      value = checked ? '1' : '0';
    }
    setSelectedData((prev) => ({ ...prev, [field]: value }));
  };

  const leftLabel = {
    fontSize: '0.75rem',
    fontWeight: 500,
    minWidth: '160px',
    marginLeft: '2px',
  };

  return (
    <>
      {/* Row 1 */}
      <CRow>
        {/* Left column: selects (unchanged) */}
        <CCol xs={6}>
          <CCard className="mb-4">
            <CCardBody>
              {/* Customer Type — inline */}
              <div style={{ ...rowStyle, gap: '12px' }} className="mb-3">
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

              {/* Return Status — inline */}
              <div style={{ ...rowStyle, gap: '12px' }} className="mb-3">
                <div id="return-status-inline-label" style={leftLabel}>Return Status</div>
                <FormControl fullWidth size="small" disabled={!isEditable} sx={{ flex: 1 }}>
                  <Select
                    id="return-status"
                    aria-labelledby="return-status-inline-label"
                    value={returnStatus}
                    onChange={handleChange('returnStatus')}
                    sx={{ fontSize: '0.78rem' }}
                  >
                    <MenuItem value="" sx={{ fontSize: '0.78rem' }}>None</MenuItem>
                    <MenuItem value="A" sx={{ fontSize: '0.78rem' }}>A Status</MenuItem>
                    <MenuItem value="C" sx={{ fontSize: '0.78rem' }}>C Status</MenuItem>
                    <MenuItem value="E" sx={{ fontSize: '0.78rem' }}>E Status</MenuItem>
                    <MenuItem value="F" sx={{ fontSize: '0.78rem' }}>F Status</MenuItem>
                  </Select>
                </FormControl>
              </div>

              {/* Destroy Status — inline */}
              <div style={{ ...rowStyle, gap: '12px' }} className="mb-3">
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

              {/* Special — inline */}
              <div style={{ ...rowStyle, gap: '12px' }} className="mb-3">
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

              {/* Pin Mailer — inline */}
              <div style={{ ...rowStyle, gap: '12px' }}>
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
            </CCardBody>
          </CCard>
        </CCol>

        {/* Right column: ONLY "AAAA" */}
        <CCol xs={6}>
          <CCard className="mb-4">
            <CCardBody>
              <div style={{ ...rowStyle, height: 'auto' }}>
                <span style={{ fontSize: '0.9rem', fontWeight: 600 }}>AAAA</span>
              </div>
            </CCardBody>
          </CCard>
        </CCol>
      </CRow>

      {/* Row 2 */}
      <CRow>
        {/* Left column: remaining four checkboxes */}
        <CCol xs={6}>
          <CCard className="mb-4">
            <CCardBody>
              <div style={rowStyle} className="mb-3">
                <CFormCheck
                  type="checkbox"
                  id="rps-customer"
                  label={<span style={labelStyle}>RPS Customer</span>}
                  checked={rps === 'Y'}
                  onChange={handleCheckboxChange('rps')}
                  disabled={!isEditable}
                />
              </div>
              <div style={rowStyle} className="mb-3">
                <CFormCheck
                  type="checkbox"
                  id="flag-undeliverable"
                  label={<span style={labelStyle}>Flag Undeliverable an Invalid Address</span>}
                  checked={addrFlag === 'Y'}
                  onChange={handleCheckboxChange('addrFlag')}
                  disabled={!isEditable}
                />
              </div>
              <div style={rowStyle} className="mb-3">
                <CFormCheck
                  type="checkbox"
                  id="status-research"
                  label={<span style={labelStyle}>A Status Accounts Going in Research</span>}
                  checked={astatRch === '1'}
                  onChange={handleCheckboxChange('astatRch')}
                  disabled={!isEditable}
                />
              </div>
              <div style={rowStyle}>
                <CFormCheck
                  type="checkbox"
                  id="perform-non-mon"
                  label={<span style={labelStyle}>Perform Non Mon 13 on Destroy</span>}
                  checked={nm13 === '1'}
                  onChange={handleCheckboxChange('nm13')}
                  disabled={!isEditable}
                />
              </div>
            </CCardBody>
          </CCard>
        </CCol>

        {/* Right column: moved "Sys/PRIN Active" checkbox */}
        <CCol xs={6}>
          <CCard className="mb-4">
            <CCardBody>
              <div style={rowStyle} className="mb-3">
                <CFormCheck
                  type="checkbox"
                  id="sys-prin-active"
                  label={<span style={labelStyle}>Sys/PRIN Active</span>}
                  checked={active === 'Y'}
                  onChange={handleCheckboxChange('active')}
                  disabled={!isEditable}
                />
              </div>
            </CCardBody>
          </CCard>
        </CCol>
      </CRow>
    </>
  );
};

export default SysPrinGeneral;
