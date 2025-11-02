import React, { useMemo, useState } from 'react';
import { CCard, CCardBody, CCol, CRow, CFormCheck } from '@coreui/react';
import TextField from '@mui/material/TextField';
import Select from '@mui/material/Select';
import MenuItem from '@mui/material/MenuItem';
import FormControl from '@mui/material/FormControl';
import Button from '@mui/material/Button';

import '../../../../scss/sys-prin-configuration/client-atm-pin-prefixes.scss';

const rowStyle = { display: 'flex', alignItems: 'center', height: '50px' };
const font78 = { fontSize: '0.78rem' };
const labelStyle = { margin: 0, fontSize: '0.78rem' };

// helpers
const as01 = (v) => (v === true || v === 1 || v === '1' || v === 'Y' ? '1' : '0');
const asYN = (v) => (v === true || v === 'Y' || v === '1' ? 'Y' : 'N');

const EditSysPrinGeneral = ({ selectedData, setSelectedData, isEditable, onChangeGeneral }) => {
  const [updating, setUpdating] = useState(false);

  // normalize patch and MIRROR contact/phone keys both ways
  const pushGeneralPatch = (patch) => {
    const n = { ...patch };

    // mirror contact <-> sysPrinContact
    if ('contact' in n && !('sysPrinContact' in n)) n.sysPrinContact = n.contact;
    if ('sysPrinContact' in n && !('contact' in n)) n.contact = n.sysPrinContact;

    // mirror phone <-> sysPrinPhone
    if ('phone' in n && !('sysPrinPhone' in n)) n.sysPrinPhone = n.phone;
    if ('sysPrinPhone' in n && !('phone' in n)) n.phone = n.sysPrinPhone;

    // keep flag types consistent
    if ('active' in n)    n.active    = as01(n.active);
    if ('astatRch' in n)  n.astatRch  = as01(n.astatRch);
    if ('nm13' in n)      n.nm13      = as01(n.nm13);
    if ('rps' in n)       n.rps       = asYN(n.rps);
    if ('addrFlag' in n)  n.addrFlag  = as01(n.addrFlag); // this one is 0/1 in your UI

    const withKey = {
      client: selectedData?.client,
      sysPrin: selectedData?.sysPrin,
      ...n,
    };

    if (typeof onChangeGeneral === 'function') onChangeGeneral(withKey);
    else setSelectedData((prev) => ({ ...(prev ?? {}), ...withKey }));
  };

  const updateField = (field) => (value) => pushGeneralPatch({ [field]: value });
  const getvalue = (field, fallback = '') => selectedData?.[field] ?? fallback;

  const custType      = selectedData?.custType || '';
  const returnStatus  = selectedData?.returnStatus || '';
  const destroyStatus = selectedData?.destroyStatus || '';
  const special       = selectedData?.special || '';
  const pinMailer     = selectedData?.pinMailer || '';

  const active01  = as01(selectedData?.active);
  const rpsYN     = asYN(selectedData?.rps);
  const addr01    = as01(selectedData?.addrFlag);
  const astat01   = as01(selectedData?.astatRch);
  const nm1301    = as01(selectedData?.nm13);

  const handleChange = (field) => (e) => {
    const value = e.target.value;
    pushGeneralPatch({ [field]: value });
  };

  const handleCheckboxChange = (field) => (e) => {
    const checked = e.target.checked;
    if (field === 'rps') {
      pushGeneralPatch({ rps: checked ? 'Y' : 'N' });
    } else if (field === 'addrFlag') {
      pushGeneralPatch({ addrFlag: checked ? '1' : '0' });
    } else {
      // active, astatRch, nm13 → '1'/'0'
      pushGeneralPatch({ [field]: checked ? '1' : '0' });
    }
  };

  const leftLabel = { fontSize: '0.75rem', fontWeight: 500, minWidth: '60px', marginLeft: '2px' };

  const buildPayload = useMemo(() => {
    const sd = selectedData ?? {};
    // read from either key; prefer canonical contact/phone when present
    const contact = (sd.contact ?? sd.sysPrinContact) ?? '';
    const phone   = (sd.phone   ?? sd.sysPrinPhone)   ?? '';

    return {
      client: sd.client ?? '',
      sysPrin: sd.sysPrin ?? '',
      custType: sd.custType ?? '0',
      returnStatus: sd.returnStatus ?? '',
      destroyStatus: sd.destroyStatus ?? '0',
      special: sd.special ?? '0',
      pinMailer: sd.pinMailer ?? '0',

      active: as01(sd.active),
      rps: asYN(sd.rps),
      addrFlag: as01(sd.addrFlag),
      astatRch: as01(sd.astatRch),
      nm13: as01(sd.nm13),

      notes: sd.notes ?? '',
      undeliverable: sd.undeliverable ?? '0',
      poBox: sd.poBox ?? '0',
      tempAway: sd.tempAway ?? 0,
      tempAwayAtts: sd.tempAwayAtts ?? 0,
      reportMethod: sd.reportMethod ?? 0,
      nonUS: sd.nonUS ?? '0',
      holdDays: sd.holdDays ?? 0,
      forwardingAddress: sd.forwardingAddress ?? '0',

      // send canonical names to backend
      contact,
      phone,

      entityCode: sd.entityCode ?? '0',
      session: sd.session ?? '',
      badState: sd.badState ?? '0',
      statA: sd.statA ?? '0',
      statB: sd.statB ?? '0',
      statC: sd.statC ?? '0',
      statD: sd.statD ?? '0',
      statE: sd.statE ?? '0',
      statF: sd.statF ?? '0',
      statI: sd.statI ?? '0',
      statL: sd.statL ?? '0',
      statO: sd.statO ?? '0',
      statU: sd.statU ?? '0',
      statX: sd.statX ?? '0',
      statZ: sd.statZ ?? '0',
    };
  }, [selectedData]);

  const handleUpdate = async () => {
    const client = selectedData?.client;
    const sysPrinCode = selectedData?.sysPrin;
    if (!client || !sysPrinCode) {
      alert('Missing client or sysPrin.');
      return;
    }

    const url = `http://localhost:8089/client-sysprin-writer/api/sysprins/update/${encodeURIComponent(client)}/${encodeURIComponent(sysPrinCode)}`;

    setUpdating(true);
    try {
      const res = await fetch(url, {
        method: 'PUT',
        headers: { accept: '*/*', 'Content-Type': 'application/json' },
        body: JSON.stringify(buildPayload),
      });

      if (!res.ok) {
        let msg = `Update failed (${res.status})`;
        try {
          const ct = res.headers.get('Content-Type') || '';
          if (ct.includes('application/json')) {
            const j = await res.json();
            msg = j?.message || JSON.stringify(j);
          } else {
            msg = await res.text();
          }
        } catch {}
        throw new Error(msg);
      }

      let saved = null;
      try { saved = await res.json(); } catch {}
      const canonical = saved || buildPayload;

      // 🔁 IMPORTANT: mirror contact/phone to sysPrinContact/sysPrinPhone for parent/UI
      const broadcast = {
        ...canonical,
        sysPrinContact: canonical.contact ?? selectedData?.sysPrinContact ?? '',
        sysPrinPhone:   canonical.phone   ?? selectedData?.sysPrinPhone   ?? '',
      };

      // ✅ after PUT, push canonical+mirrored patch up (keeps grid & preview consistent)
      pushGeneralPatch(broadcast);

      alert('Sys/PRIN updated successfully.');
    } catch (e) {
      console.error(e);
      alert(e?.message || 'Failed to update.');
    } finally {
      setUpdating(false);
    }
  };

  return (
    <CRow>
      <CCol xs={6}>
        <CCard className="mb-4">
          <CCardBody>
            {/* Customer Type */}
            <div style={{ ...rowStyle, gap: '12px' }} className="mb-3">
              <div id="customer-type-inline-label" style={leftLabel}>Customer Type</div>
              <FormControl fullWidth size="small" disabled={!isEditable} sx={{ flex: 1 }}>
                <Select id="customer-type" aria-labelledby="customer-type-inline-label"
                        value={custType} onChange={handleChange('custType')} sx={{ fontSize: '0.78rem' }}>
                  <MenuItem value="0" sx={{ fontSize: '0.78rem' }}><em>None</em></MenuItem>
                  <MenuItem value="1" sx={{ fontSize: '0.78rem' }}>Full Processing</MenuItem>
                  <MenuItem value="2" sx={{ fontSize: '0.78rem' }}>Destroy All</MenuItem>
                  <MenuItem value="3" sx={{ fontSize: '0.78rem' }}>Return All</MenuItem>
                </Select>
              </FormControl>
            </div>

            {/* Return Status */}
            <div style={{ ...rowStyle, gap: '12px' }} className="mb-3">
              <div id="return-status-inline-label" style={leftLabel}>Return Status</div>
              <FormControl fullWidth size="small" disabled={!isEditable} sx={{ flex: 1 }}>
                <Select id="return-status" aria-labelledby="return-status-inline-label"
                        value={returnStatus} onChange={handleChange('returnStatus')} sx={{ fontSize: '0.78rem' }}>
                  <MenuItem value=""  sx={{ fontSize: '0.78rem' }}>None</MenuItem>
                  <MenuItem value="A" sx={{ fontSize: '0.78rem' }}>A Status</MenuItem>
                  <MenuItem value="C" sx={{ fontSize: '0.78rem' }}>C Status</MenuItem>
                  <MenuItem value="E" sx={{ fontSize: '0.78rem' }}>E Status</MenuItem>
                  <MenuItem value="F" sx={{ fontSize: '0.78rem' }}>F Status</MenuItem>
                </Select>
              </FormControl>
            </div>

            {/* Destroy Status */}
            <div style={{ ...rowStyle, gap: '12px' }} className="mb-3">
              <div id="destroy-status-inline-label" style={leftLabel}>Destroy Status</div>
              <FormControl fullWidth size="small" disabled={!isEditable} sx={{ flex: 1 }}>
                <Select id="destroy-status" aria-labelledby="destroy-status-inline-label"
                        value={destroyStatus} onChange={handleChange('destroyStatus')} sx={{ fontSize: '0.78rem' }}>
                  <MenuItem value="0" sx={{ fontSize: '0.78rem' }}>None</MenuItem>
                  <MenuItem value="1" sx={{ fontSize: '0.78rem' }}>Destroy</MenuItem>
                  <MenuItem value="2" sx={{ fontSize: '0.78rem' }}>Return</MenuItem>
                </Select>
              </FormControl>
            </div>

            {/* Special */}
            <div style={{ ...rowStyle, gap: '12px' }} className="mb-3">
              <div id="special-inline-label" style={leftLabel}>Special</div>
              <FormControl fullWidth size="small" disabled={!isEditable} sx={{ flex: 1 }}>
                <Select id="special-option" aria-labelledby="special-inline-label"
                        value={special} onChange={handleChange('special')} sx={{ fontSize: '0.78rem' }}>
                  <MenuItem value="0" sx={{ fontSize: '0.78rem' }}>None</MenuItem>
                  <MenuItem value="1" sx={{ fontSize: '0.78rem' }}>Destroy</MenuItem>
                  <MenuItem value="2" sx={{ fontSize: '0.78rem' }}>Return</MenuItem>
                </Select>
              </FormControl>
            </div>

            {/* Pin Mailer */}
            <div style={{ ...rowStyle, gap: '12px' }}>
              <div id="pin-mailer-inline-label" style={leftLabel}>Pin Mailer</div>
              <FormControl fullWidth size="small" disabled={!isEditable} sx={{ flex: 1 }}>
                <Select id="pin-mailer" aria-labelledby="pin-mailer-inline-label"
                        value={pinMailer} onChange={handleChange('pinMailer')} sx={{ fontSize: '0.78rem' }}>
                  <MenuItem value="0" sx={{ fontSize: '0.78rem' }}>Non</MenuItem>
                  <MenuItem value="1" sx={{ fontSize: '0.78rem' }}>Destroy</MenuItem>
                  <MenuItem value="2" sx={{ fontSize: '0.78rem' }}>Return</MenuItem>
                </Select>
              </FormControl>
            </div>
          </CCardBody>
        </CCard>
      </CCol>

      <CCol xs={6}>
        <CCard className="mb-4">
          <CCardBody>
            {/* Contact / Phone */}
            <CRow className="mb-3 align-items-center">
              <CCol xs={6}>
                <div style={{ ...rowStyle, height: 'auto' }}>
                  <div style={leftLabel}>Contact</div>
                  <TextField
                    variant="outlined" fullWidth size="small"
                    value={getvalue('sysPrinContact')}
                    onChange={(e) => updateField('sysPrinContact')(e.target.value)}
                    InputProps={{ sx: font78 }} disabled={!isEditable}
                  />
                </div>
              </CCol>
              <CCol xs={6}>
                <div style={{ ...rowStyle, height: 'auto' }}>
                  <div style={leftLabel}>Phone</div>
                  <TextField
                    variant="outlined" fullWidth size="small"
                    value={getvalue('sysPrinPhone')}
                    onChange={(e) => updateField('sysPrinPhone')(e.target.value)}
                    InputProps={{ sx: font78 }} disabled={!isEditable}
                  />
                </div>
              </CCol>
            </CRow>

            {/* Checkboxes */}
            <CRow className="mb-3 align-items-center">
              <CCol xs={6}>
                <div style={rowStyle}>
                  <CFormCheck
                    type="checkbox" id="rps-customer"
                    label={<span style={labelStyle}>RPS Customer</span>}
                    checked={rpsYN === 'Y'} onChange={handleCheckboxChange('rps')}
                    disabled={!isEditable}
                  />
                </div>
              </CCol>
              <CCol xs={6}>
                <div style={{ ...rowStyle, height: 'auto' }}>
                  <CFormCheck
                    type="checkbox" id="sys-prin-active"
                    label={<span style={labelStyle}>Sys/PRIN Active</span>}
                    checked={active01 === '1'} onChange={handleCheckboxChange('active')}
                    disabled={!isEditable}
                  />
                </div>
              </CCol>
            </CRow>

            <div style={rowStyle} className="mb-3">
              <CFormCheck
                type="checkbox" id="flag-undeliverable"
                label={<span style={labelStyle}>Flag Undeliverable an Invalid Address</span>}
                checked={addr01 === '1'} onChange={handleCheckboxChange('addrFlag')}
                disabled={!isEditable}
              />
            </div>
            <div style={rowStyle} className="mb-3">
              <CFormCheck
                type="checkbox" id="status-research"
                label={<span style={labelStyle}>A Status Accounts Going in Research</span>}
                checked={astat01 === '1'} onChange={handleCheckboxChange('astatRch')}
                disabled={!isEditable}
              />
            </div>
            <div style={rowStyle}>
              <CFormCheck
                type="checkbox" id="perform-non-mon"
                label={<span style={labelStyle}>Perform Non Mon 13 on Destroy</span>}
                checked={nm1301 === '1'} onChange={handleCheckboxChange('nm13')}
                disabled={!isEditable}
              />
            </div>

            <div style={{ display: 'flex', justifyContent: 'flex-end', marginTop: 16 }}>
              <Button
                variant="contained" size="small" onClick={handleUpdate}
                disabled={updating || !isEditable || !selectedData?.client || !selectedData?.sysPrin}
              >
                {updating ? 'Updating…' : 'Update'}
              </Button>
            </div>

          </CCardBody>
        </CCard>
      </CCol>
    </CRow>
  );
};

export default EditSysPrinGeneral;
