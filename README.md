import { useMemo, useState } from 'react';
import { CCard, CCardBody, CCol, CRow, CFormCheck } from '@coreui/react';
import { FormControl, Select, MenuItem } from '@mui/material';


// import '../../../../scss/sys-prin-configuration/client-atm-pin-prefixes.scss';

const rowStyle = { display: 'flex', alignItems: 'center', height: '50px' };
const font78 = { fontSize: '0.78rem' };
const labelStyle = { margin: 0, fontSize: '0.78rem' };

const EditSysPrinGeneral = ({ selectedData, setSelectedData, isEditable, onChangeGeneral }) => {
  const [updating, setUpdating] = useState(false);

 const pushGeneralPatch = (patch) => {
   const withKey = {
     client: selectedData?.client,     // add this
     sysPrin: selectedData?.sysPrin,   // keep this
     ...patch
   };
   console.log('[General] patch ->', withKey);
   if (typeof onChangeGeneral === 'function') onChangeGeneral(withKey);
   else setSelectedData((prev) => ({ ...(prev ?? {}), ...withKey }));
 };

  const custType     = selectedData?.custType || '';
  const returnStatus = selectedData?.returnStatus || '';
  const destroyStatus= selectedData?.destroyStatus || '';
  const special      = selectedData?.special || '';
  const pinMailer    = selectedData?.pinMailer || '';
  const sysPrinActive       = selectedData?.sysPrinActive === true || selectedData?.sysPrinActive === '1' ? '1' : '0';
  const rps          = selectedData?.rps === true || selectedData?.rps === '1' ? '1' : '0';
  const addrFlag     = selectedData?.addrFlag === true || selectedData?.addrFlag === '1' ? '1' : '0';
  const astatRch     = selectedData?.astatRch === true || selectedData?.astatRch === '1' ? '1' : '0';
  const nm13         = selectedData?.nm13 === true || selectedData?.nm13 === '1' ? '1' : '0';
  

  const handleChange = (field) => (e) => {
    const value = e.target.value;
    pushGeneralPatch({ [field]: value });
  };

  const handleCheckboxChange = (field) => (e) => {
    const checked = e.target.checked;
    let value = '';
    if (['astatRch', 'nm13', 'addrFlag', 'sysPrinActive', 'rps'].includes(field))     value = checked ? '1' : '0';
    pushGeneralPatch({ [field]: value });
  };

  const leftLabel = { fontSize: '0.75rem', fontWeight: 500, minWidth: '60px', marginLeft: '2px' };

  const buildPayload = useMemo(() => {
    const sd   = selectedData ?? {};
    const toB  = (v) => (v === true || v === 'Y');
    const to10 = (v) => (v === true || v === '1') ? '1' : (v === '0' || v === false ? '0' : (v ?? '0'));
    const toYN = (v) => (v === true || v === 'Y') ? 'Y' : (v === false || v === 'N' ? 'N' : (v ?? 'N'));
    return {
      client: sd.client ?? '',
      sysPrin: sd.sysPrin ?? '',
      custType: sd.custType ?? '0',
      returnStatus: sd.returnStatus ?? '',
      destroyStatus: sd.destroyStatus ?? '0',
      special: sd.special ?? '0',
      pinMailer: sd.pinMailer ?? '0',
      sysPrinActive: to10(sd.sysPrinActive),
      rps: to10(sd.rps),
      addrFlag: to10(sd.addrFlag),
      astatRch: to10(sd.astatRch),
      nm13: to10(sd.nm13),
      notes: sd.notes ?? '',
      undeliverable: sd.undeliverable ?? '0',
      poBox: sd.poBox ?? '0',
      tempAway: sd.tempAway ?? 0,
      tempAwayAtts: sd.tempAwayAtts ?? 0,
      reportMethod: sd.reportMethod ?? 0,
      nonUS: sd.nonUS ?? '0',
      holdDays: sd.holdDays ?? 0,
      forwardingAddress: sd.forwardingAddress ?? '0',
      sysPrinContact: sd.sysPrinContact ?? '',
      sysPrinPhone: sd.sysPrinPhone ?? '',
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

      // ✅ after PUT, push canonical patch up (keeps list/grid consistent)
      pushGeneralPatch(canonical);

      alert('Sys/PRIN updated successfully.');
    } catch (e) {
      console.error(e);
      alert(e?.message || 'Failed to update.');
    } finally {
      setUpdating(false);
    }
  };

  return (
<CCol xs={12}>
  <CCard className="mb-4">
    <CCardBody>

      {/* Row 1 — 4 columns of selects */}
      <CRow className="mb-3">
        {/* Customer Type */}
        <CCol xs={3}>
          <div style={{ ...rowStyle, gap: '12px', height: 'auto' }}>
            <FormControl fullWidth size="small" disabled={!isEditable} sx={{ flex: 1 }}>
              <label
                htmlFor="customer-type"
                style={{
                  fontSize: '0.78rem',
                  marginBottom: '4px',
                  display: 'flex',
                  alignItems: 'center',
                  gap: 6,
                }}
              >
                Customer Type
              </label>
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
            <FormControl fullWidth size="small" disabled={!isEditable} sx={{ flex: 1 }}>
              <label
                htmlFor="return-status"
                style={{
                  fontSize: '0.78rem',
                  marginBottom: '4px',
                  display: 'flex',
                  alignItems: 'center',
                  gap: 6,
                }}
              >
                Return Status
              </label>
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
            <FormControl fullWidth size="small" disabled={!isEditable} sx={{ flex: 1 }}>
              <label
                htmlFor="destroy-status"
                style={{
                  fontSize: '0.78rem',
                  marginBottom: '4px',
                  display: 'flex',
                  alignItems: 'center',
                  gap: 6,
                }}
              >
                Destroy Status
              </label>
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
            <FormControl fullWidth size="small" disabled={!isEditable} sx={{ flex: 1 }}>
              <label
                htmlFor="special-option"
                style={{
                  fontSize: '0.78rem',
                  marginBottom: '4px',
                  display: 'flex',
                  alignItems: 'center',
                  gap: 6,
                }}
              >
                Special
              </label>
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
            <FormControl fullWidth size="small" disabled={!isEditable} sx={{ flex: 1 }}>
              <label
                htmlFor="pin-mailer-option"
                style={{
                  fontSize: '0.78rem',
                  marginBottom: '4px',
                  display: 'flex',
                  alignItems: 'center',
                  gap: 6,
                }}
              >
                Pin Mailer
              </label>
              <Select
                id="pin-mailer-option"
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
      </CRow>
      <CRow>
        {/* astatRch */}
        <CCol xs={3} className="mb-3"  style={{ display: 'flex', alignItems: 'center', justifyContent: 'flex-start', paddingLeft: '4px', flex: '0 0 40%', maxWidth: '40%' }} >
          <div style={rowStyle}>
            <CFormCheck
              type="checkbox"
              id="astatRch"
              label={<span style={labelStyle}>A Status Accounts Going in Research</span>}
              checked={astatRch === '1'}
              onChange={handleCheckboxChange('astatRch')}
              disabled={!isEditable}
            />
          </div>
        </CCol>

        {/* RPS Customer */}
        <CCol xs={3} className="mb-3"  style={{ display: 'flex', alignItems: 'center', justifyContent: 'flex-start', paddingLeft: '4px', flex: '0 0 30%', maxWidth: '30%' }} >
          <div style={rowStyle}>
            <CFormCheck
              type="checkbox"
              id="rps-customer"
              label={<span style={labelStyle}>RPS Customer</span>}
              checked={rps === '1'}
              onChange={handleCheckboxChange('rps')}
              disabled={!isEditable}
            />
          </div>
        </CCol>

        {/* SysPrin Active */}
        <CCol xs={3} className="mb-3" style={{ display: 'flex', alignItems: 'center', justifyContent: 'flex-start', paddingLeft: '4px', flex: '0 0 30%', maxWidth: '30%' }}>
          <div style={{ ...rowStyle, height: 'auto' }}>
            <CFormCheck
              type="checkbox"
              id="sys-prin-active"
              label={<span style={labelStyle}>SysPrin Active</span>}
              checked={sysPrinActive === '1'}
              onChange={handleCheckboxChange('sysPrinActive')}
              disabled={!isEditable}
            />
          </div>
        </CCol>
     </CRow>
    <CRow>
        {/* Stacked long-label checks */}
        <CCol xs={3} style={{ display: 'flex', alignItems: 'center', justifyContent: 'flex-start', paddingLeft: '4px', flex: '0 0 40%', maxWidth: '40%' }}>
          <div style={{ display: 'flex', flexDirection: 'column', gap: 8 }}>
            <CFormCheck
              type="checkbox"
              id="flag-undeliverable"
              label={<span style={labelStyle}>Flag Undeliverable an Invalid Address</span>}
              checked={addrFlag === '1'}
              onChange={handleCheckboxChange('addrFlag')}
              disabled={!isEditable}
            />
          </div>
        </CCol>
        <CCol xs={3}  style={{ display: 'flex', alignItems: 'center', justifyContent: 'flex-start', paddingLeft: '4px', flex: '0 0 40%', maxWidth: '40%' }}>
          <div style={{ display: 'flex', flexDirection: 'column', gap: 8 }}>
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

      {/* Update button aligned right
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
     */}
    </CCardBody>
  </CCard>
</CCol>
);
};

export default EditSysPrinGeneral;
