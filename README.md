// EditStatusOptions.jsx
import React, { useMemo, useState } from 'react';
import { CCard, CCardBody, CCol, CRow } from '@coreui/react';
import { Select, MenuItem, FormControl, Button } from '@mui/material';

const font78 = { fontSize: '0.78rem' };

const BADGE_COLORS = { a:'#1976d2', b:'#9c27b0', c:'#2e7d32', d:'#ef6c00', e:'#d32f2f', f:'#455a64', i:'#6d4c41', l:'#0288d1', o:'#c2185b', u:'#00796b', x:'#5d4037', z:'#7b1fa2' };
const LEFT_STATUSES  = ['a','b','c','d','e','f'];
const RIGHT_STATUSES = ['i','l','o','u','x','z'];

const OPTIONS = {
  '0':'Destroy',
  '1':'Return',
  '2':'Research / Destroy',
  '3':'Research / Return',
  '4':'Research / Carrier Ret',
};

const DROPDOWN_OPTIONS_MAP = {
  a:['0','1','2','3','4'], b:['0','1'], c:['0','1','2','3','4'],
  d:['0','1','2','3','4'], e:['0','1','2','3','4'], f:['0','1','2','3','4'],
  i:['0','1','2','3','4'], l:['0','1'], o:['0','1','2','3','4'],
  u:['0','1'], x:['0','1','2','3','4'], z:['0','1'],
};

const EditStatusOptions = ({
  selectedData = {},
  statusMap = {},
  setStatusMap,
  isEditable,
  // ⬅️ NEW: parent updater to keep selectedData / grid in sync
  onChangeGeneral,
}) => {
  const [updating, setUpdating] = useState(false);

  // helper to bubble a patch upward
  const pushGeneralPatch = (patch) => {
    if (typeof onChangeGeneral === 'function') {
      const withKeys = {
        client: selectedData?.client,
        sysPrin: selectedData?.sysPrin,
        ...patch,
      };
      onChangeGeneral(withKeys);
    }
  };

  const handleChange = (key, rawValue) => {
    const statusKey = `stat${key.toUpperCase()}`;
    const value = rawValue === null || rawValue === undefined || rawValue === '' ? '' : String(rawValue);
    setStatusMap((prev) => ({ ...prev, [statusKey]: value }));
  };

  const renderSelect = (key) => {
    const statusKey = `stat${key.toUpperCase()}`;
    const raw = statusMap?.[statusKey] ?? selectedData?.[statusKey] ?? '';
    const value = raw === null || raw === undefined || raw === '' ? '' : String(raw);

    return (
      <div key={key} className="d-flex align-items-center mb-1" style={{ gap:'0px', marginBottom:'2px' }}>
        <FormControl size="small" sx={{ mb:'0px', width:'100%' }}>
          <div style={{ fontSize:'0.75rem', fontWeight:500, marginBottom:'1px', marginLeft:'2px' }}>
            {`${key.toUpperCase()} Status`}
          </div>

          <Select
            labelId={`${statusKey}-label`}
            id={statusKey}
            value={value}
            onChange={(e) => handleChange(key, e.target.value)}
            sx={font78}
            disabled={!isEditable}
            displayEmpty
          >
            <MenuItem value="" sx={font78}><em>None</em></MenuItem>
            {DROPDOWN_OPTIONS_MAP[key].map((code) => (
              <MenuItem key={code} value={code} sx={font78}>
                {OPTIONS[code]}
              </MenuItem>
            ))}
          </Select>
        </FormControl>
      </div>
    );
  };

  const renderSelectDescription = (key) => {
    const bg = BADGE_COLORS[key] || '#9e9e9e';
    return (
      <div key={`desc-${key}`} className="d-flex align-items-center mb-1" style={{ gap:'6px', marginBottom:'8px', height:'32px' }}>
        <div style={{
          width:'15px', height:'15px', backgroundColor:bg, color:'white', fontWeight:'bold',
          borderRadius:'3px', display:'flex', alignItems:'center', justifyContent:'center', fontSize:'0.78rem'
        }}>
          {key.toUpperCase()}
        </div>
        <p style={{ margin:0, fontSize:'0.78rem' }}>Status</p>
      </div>
    );
  };

  const buildPayload = useMemo(() => {
    const sd = selectedData ?? {};
    const toBool = (v) => (v === true || v === 'Y');
    const to10  = (v) => (v === true || v === '1') ? '1' : (v === '0' || v === false ? '0' : (v ?? '0'));
    const toYN  = (v) => (v === true || v === 'Y') ? 'Y' : (v === false || v === 'N' ? 'N' : (v ?? 'N'));

    const stat = (k) => {
      const key = `stat${k}`;
      const override = statusMap?.[key];
      return (override === undefined || override === null) ? (sd?.[key] ?? '0') : override;
    };

    return {
      client: sd.client ?? '',
      sysPrin: sd.sysPrin ?? '',
      custType: sd.custType ?? '0',
      returnStatus: sd.returnStatus ?? '',
      destroyStatus: sd.destroyStatus ?? '0',
      special: sd.special ?? '0',
      pinMailer: sd.pinMailer ?? '0',
      active: toBool(sd.active),
      rps: toYN(sd.rps),
      addrFlag: toYN(sd.addrFlag),
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
      contact: sd.contact ?? '',
      phone: sd.phone ?? '',
      entityCode: sd.entityCode ?? '0',
      session: sd.session ?? '',
      badState: sd.badState ?? '0',
      statA: stat('A'), statB: stat('B'), statC: stat('C'),
      statD: stat('D'), statE: stat('E'), statF: stat('F'),
      statI: stat('I'), statL: stat('L'), statO: stat('O'),
      statU: stat('U'), statX: stat('X'), statZ: stat('Z'),
    };
  }, [selectedData, statusMap]);

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
        headers: { accept:'*/*', 'Content-Type':'application/json' },
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

      // If server returns canonical row, you can read and use it:
      // let saved = null;
      // try {
      //   if ((res.headers.get('Content-Type') || '').includes('application/json')) {
      //     saved = await res.json();
      //   }
      // } catch {}

      // Build the patch this tab owns (stat fields).
      const patch = {
        statA: buildPayload.statA, statB: buildPayload.statB, statC: buildPayload.statC,
        statD: buildPayload.statD, statE: buildPayload.statE, statF: buildPayload.statF,
        statI: buildPayload.statI, statL: buildPayload.statL, statO: buildPayload.statO,
        statU: buildPayload.statU, statX: buildPayload.statX, statZ: buildPayload.statZ,
      };

      // Bubble to parent so selectedData (and any grid list) is updated immediately
      pushGeneralPatch(patch);

      alert('Statuses updated successfully.');
      // Optionally clear local overrides after success:
      // setStatusMap?.({});
    } catch (e) {
      console.error(e);
      alert(e?.message || 'Failed to update.');
    } finally {
      setUpdating(false);
    }
  };

  return (
    <CRow className="g-2" style={{ border:'1px solid #ccc', borderRadius:'6px' }}>
      <CCol xs={12}>
        <CCard className="mb-2">
          <CCardBody className="py-2 px-3">
            <CRow className="g-2">
              {[...LEFT_STATUSES, ...RIGHT_STATUSES].map((statusKey) => (
                <CCol sm={3} key={statusKey}>
                  <div className="d-flex flex-column gap-2 py-2 px-1">
                    {renderSelect(statusKey)}
                    {renderSelectDescription(statusKey)}
                  </div>
                </CCol>
              ))}
            </CRow>

            <div className="d-flex justify-content-end mt-2">
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
    </CRow>
  );
};

export default EditStatusOptions;







<EditStatusOptions
  selectedData={selectedData}
  statusMap={statusMap}
  setStatusMap={setStatusMap}
  isEditable={isEditable}
  onChangeGeneral={onChangeGeneral}   // ⬅️ Pass this
/>




