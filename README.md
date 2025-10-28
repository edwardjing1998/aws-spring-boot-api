import React, { useState } from 'react';
import { CCard, CCardBody, CRow, CCol } from '@coreui/react';
import TextField from '@mui/material/TextField';

// Explicit code → label map so preview never depends on array order
const CODE_TO_LABEL = {
  '0': 'Return',
  '1': 'Destroy',
  '2': 'Research / Destroy',
  '3': 'Research / Return',
  '4': 'Research / Carrier Ret',
};

const PreviewStatusOptions = ({ selectedData, sharedSx, getStatusValue }) => {
  // existing lists (can keep for layout parity, but preview uses CODE_TO_LABEL)
  const statusOptionsA = [
    'Destroy',
    'Return',
    'Research / Destroy',
    'Research / Return',
    'Research / Carrier Ret',
  ];
  const statusOptionsB = ['Destroy', 'Return'];
  const statusOptionsC = [
    'Destroy',
    'Return',
    'Research / Destroy',
    'Research / Return',
    'Research / Carrier Ret',
  ];
  const statusOptionsD = [
    'Destroy',
    'Return',
    'Research / Destroy',
    'Research / Return',
    'Research / Carrier Ret',
  ];
  const statusOptionsE = [
    'Destroy',
    'Return',
    'Research / Destroy',
    'Research / Return',
    'Research / Carrier Ret',
  ];
  const statusOptionsF = [
    'Destroy',
    'Return',
    'Research / Destroy',
    'Research / Return',
    'Research / Carrier Ret',
  ];
  const statusOptionsI = [
    'Destroy',
    'Return',
    'Research / Destroy',
    'Research / Return',
    'Research / Carrier Ret',
  ];
  const statusOptionsL = ['Destroy', 'Return'];
  const statusOptionsO = [
    'Destroy',
    'Return',
    'Research / Destroy',
    'Research / Return',
    'Research / Carrier Ret',
  ];
  const statusOptionsU = ['Destroy', 'Return'];
  const statusOptionsX = [
    'Destroy',
    'Return',
    'Research / Destroy',
    'Research / Return',
    'Research / Carrier Ret',
  ];
  const statusOptionsZ = ['Destroy', 'Return'];

  const [isEditable] = useState(false);

  // Safe value resolver — prefer parent's helper if provided, else our code map
  const safeGetStatusValue =
    typeof getStatusValue === 'function'
      ? getStatusValue
      : (_optionList, code) => CODE_TO_LABEL[String(code ?? '')] ?? '';

  const renderStatusRow = (labels, values) => (
    <>
      <CRow style={{ height: '24px', marginBottom: '0px' }}>
        {labels.map(({ code, color }, idx) => (
          <CCol key={idx} style={{ display: 'flex', alignItems: 'center' }}>
            <div
              style={{
                width: '15px',
                height: '15px',
                backgroundColor: color,
                color: 'white',
                fontWeight: 'bold',
                borderRadius: '3px',
                display: 'flex',
                alignItems: 'center',
                justifyContent: 'center',
                fontSize: '0.78rem',
                marginRight: '5px',
              }}
            >
              {code}
            </div>
            <p style={{ margin: 0, fontSize: '0.78rem' }}>Status</p>
          </CCol>
        ))}
      </CRow>

      <CRow style={{ height: '28px', marginTop: '10px' }}>
        {values.map(({ optionList, value }, idx) => (
          <CCol key={idx} style={{ display: 'flex', alignItems: 'center' }}>
            <TextField
              placeholder="xxx"
              value={safeGetStatusValue(optionList, value)}
              size="small"
              fullWidth
              disabled={!isEditable}
              sx={{
                '& .MuiOutlinedInput-root': {
                  height: '36px',
                  fontSize: '0.78rem',
                  ...(sharedSx || {}),
                },
                '& .MuiOutlinedInput-input': {
                  padding: '10px 12px',
                },
              }}
            />
          </CCol>
        ))}
      </CRow>
    </>
  );

  return (
    <>
      <div style={{ overflow: 'visible' }}>
        <CCard
          style={{
            height: '35px',
            marginBottom: '4px',
            marginTop: '2px',
            border: 'none',
            backgroundColor: '#f3f6f8',
            boxShadow: 'none',
            borderRadius: '4px',
          }}
        >
          <CCardBody
            className="d-flex align-items-center"
            style={{
              padding: '0.25rem 0.5rem',
              height: '100%',
              backgroundColor: 'transparent',
            }}
          >
            <p style={{ margin: 0, fontSize: '0.78rem', fontWeight: '500' }}>
              General
            </p>
          </CCardBody>
        </CCard>

        <CCard
          style={{
            marginBottom: '0px',
            marginTop: '20px',
            minHeight: '100px',
            border: 'none',
            boxShadow: 'none',
            borderBottom: '1px solid #ccc',
          }}
        >
          <CCardBody
            style={{
              padding: '0.25rem 0.5rem',
              backgroundColor: 'white',
              display: 'flex',
              flexDirection: 'column',
              justifyContent: 'flex-start',
              gap: '0px',
            }}
          >
            {renderStatusRow(
              [
                { code: 'A', color: 'red' },
                { code: 'B', color: 'brown' },
                { code: 'C', color: 'green' },
                { code: 'E', color: 'black' },
              ],
              [
                { optionList: statusOptionsA, value: selectedData?.statA },
                { optionList: statusOptionsB, value: selectedData?.statB },
                { optionList: statusOptionsC, value: selectedData?.statC },
                { optionList: statusOptionsE, value: selectedData?.statE },
              ]
            )}
          </CCardBody>
        </CCard>

        <CCard
          style={{
            marginBottom: '0px',
            marginTop: '20px',
            minHeight: '100px',
            border: 'none',
            boxShadow: 'none',
            borderBottom: '1px solid #ccc',
          }}
        >
          <CCardBody
            style={{
              padding: '0.25rem 0.5rem',
              backgroundColor: 'white',
              display: 'flex',
              flexDirection: 'column',
              justifyContent: 'flex-start',
              gap: '0px',
            }}
          >
            {renderStatusRow(
              [
                { code: 'F', color: 'blue' },
                { code: 'I', color: '#f110ee' },
                { code: 'L', color: '#f17610' },
                { code: 'U', color: '#10e7f1' },
              ],
              [
                { optionList: statusOptionsF, value: selectedData?.statF },
                { optionList: statusOptionsI, value: selectedData?.statI },
                { optionList: statusOptionsL, value: selectedData?.statL },
                { optionList: statusOptionsU, value: selectedData?.statU },
              ]
            )}
          </CCardBody>
        </CCard>

        <CCard
          style={{
            marginBottom: '0px',
            marginTop: '20px',
            minHeight: '100px',
            border: 'none',
            boxShadow: 'none',
            borderBottom: '1px solid #ccc',
          }}
        >
          <CCardBody
            style={{
              padding: '0.25rem 0.5rem',
              backgroundColor: 'white',
              display: 'flex',
              flexDirection: 'column',
              justifyContent: 'flex-start',
              gap: '0px',
            }}
          >
            {renderStatusRow(
              [
                { code: 'D', color: '#8410f1' },
                { code: 'O', color: 'blue' },
                { code: 'X', color: '#65167c' },
                { code: 'Z', color: '#c07bc4' },
              ],
              [
                { optionList: statusOptionsD, value: selectedData?.statD },
                { optionList: statusOptionsO, value: selectedData?.statO },
                { optionList: statusOptionsX, value: selectedData?.statX },
                { optionList: statusOptionsZ, value: selectedData?.statZ },
              ]
            )}
          </CCardBody>
        </CCard>
      </div>
    </>
  );
};

export default PreviewStatusOptions;




import React, { useMemo, useState } from 'react';
import { CCard, CCardBody, CCol, CRow } from '@coreui/react';
import { Select, MenuItem, FormControl, Button } from '@mui/material';

const font78 = { fontSize: '0.78rem' };

// Per-status badge colors (customize as you like)
const BADGE_COLORS = {
  a: '#1976d2', // blue
  b: '#9c27b0', // purple
  c: '#2e7d32', // green
  d: '#ef6c00', // orange
  e: '#d32f2f', // red
  f: '#455a64', // blue-grey
  i: '#6d4c41', // brown
  l: '#0288d1', // light blue
  o: '#c2185b', // pink
  u: '#00796b', // teal
  x: '#5d4037', // deep brown
  z: '#7b1fa2', // deep purple
};

const LEFT_STATUSES  = ['a', 'b', 'c', 'd', 'e', 'f'];
const RIGHT_STATUSES = ['i', 'l', 'o', 'u', 'x', 'z'];

// human-readable labels
const OPTIONS = {
  '0': 'Return',
  '1': 'Destroy',
  '2': 'Research / Destroy',
  '3': 'Research / Return',
  '4': 'Research / Carrier Ret',
};

// allowed codes per status letter (kept as strings)
const DROPDOWN_OPTIONS_MAP = {
  a: ['0', '1', '2', '3', '4'],
  b: ['0', '1'],
  c: ['0', '1', '2', '3', '4'],
  d: ['0', '1', '2', '3', '4'],
  e: ['0', '1', '2', '3', '4'],
  f: ['0', '1', '2', '3', '4'],
  i: ['0', '1', '2', '3', '4'],
  l: ['0', '1'],
  o: ['0', '1', '2', '3', '4'],
  u: ['0', '1'],
  x: ['0', '1', '2', '3', '4'],
  z: ['0', '1'],
};

const EditStatusOptions = ({
  selectedData = {},
  statusMap = {},
  setStatusMap,
  isEditable,
  // NEW: bubble patches to parent (same contract as General/ReMail)
  onChangeGeneral,
}) => {
  const [updating, setUpdating] = useState(false);

  // central helper — bubble to parent or fallback (if needed)
  const pushGeneralPatch = (patch) => {
    const withKeys = {
      client: selectedData?.client,
      sysPrin: selectedData?.sysPrin,
      ...patch,
    };
    if (typeof onChangeGeneral === 'function') onChangeGeneral(withKeys);
  };

  const handleChange = (key, rawValue) => {
    const statusKey = `stat${key.toUpperCase()}`;
    const value =
      rawValue === null || rawValue === undefined || rawValue === ''
        ? ''
        : String(rawValue);
    setStatusMap((prev) => ({ ...prev, [statusKey]: value }));
  };

  const renderSelect = (key) => {
    const statusKey = `stat${key.toUpperCase()}`;
    const raw = statusMap?.[statusKey] ?? selectedData?.[statusKey] ?? '';
    const value =
      raw === null || raw === undefined || raw === '' ? '' : String(raw);

    return (
      <div
        key={key}
        className="d-flex align-items-center mb-1"
        style={{ gap: '0px', marginBottom: '2px' }}
      >
        <FormControl size="small" sx={{ mb: '0px', width: '100%' }}>
          <div
            style={{
              fontSize: '0.75rem',
              fontWeight: 500,
              marginBottom: '1px',
              marginLeft: '2px',
            }}
          >
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
            <MenuItem value="" sx={font78}>
              <em>None</em>
            </MenuItem>

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
    const bg = BADGE_COLORS[key] || '#9e9e9e'; // fallback grey
    return (
      <div
        key={`desc-${key}`}
        className="d-flex align-items-center mb-1"
        style={{ gap: '6px', marginBottom: '8px', height: '32px' }}
      >
        <div
          style={{
            width: '15px',
            height: '15px',
            backgroundColor: bg,
            color: 'white',
            fontWeight: 'bold',
            borderRadius: '3px',
            display: 'flex',
            alignItems: 'center',
            justifyContent: 'center',
            fontSize: '0.78rem',
          }}
        >
          {key.toUpperCase()}
        </div>
        <p style={{ margin: 0, fontSize: '0.78rem' }}>Status</p>
      </div>
    );
  };

  // ------------ Build payload (same strategy as SysPrinGeneral/ReMail) ------------
  const buildPayload = useMemo(() => {
    const sd = selectedData ?? {};
    const toBool = (v) => v === true || v === 'Y';
    const to10 = (v) =>
      v === true || v === '1'
        ? '1'
        : v === '0' || v === false
        ? '0'
        : v ?? '0';
    const toYN = (v) =>
      v === true || v === 'Y'
        ? 'Y'
        : v === false || v === 'N'
        ? 'N'
        : v ?? 'N';

    // Merge stat* from statusMap over selectedData
    const stat = (k) => {
      const key = `stat${k}`;
      const override = statusMap?.[key];
      return override === undefined || override === null ? sd?.[key] ?? '0' : override;
    };

    return {
      client: sd.client ?? '',
      sysPrin: sd.sysPrin ?? '',

      // keep existing values (not edited here)
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
      reportMethod: Number(sd.reportMethod ?? 0),
      nonUS: sd.nonUS ?? '0',
      holdDays: sd.holdDays ?? 0,
      forwardingAddress: sd.forwardingAddress ?? '0',
      contact: sd.contact ?? '',
      phone: sd.phone ?? '',
      entityCode: sd.entityCode ?? '0',
      session: sd.session ?? '',
      badState: sd.badState ?? '0',

      // status letters (overridden by UI changes if present)
      statA: stat('A'),
      statB: stat('B'),
      statC: stat('C'),
      statD: stat('D'),
      statE: stat('E'),
      statF: stat('F'),
      statI: stat('I'),
      statL: stat('L'),
      statO: stat('O'),
      statU: stat('U'),
      statX: stat('X'),
      statZ: stat('Z'),
    };
  }, [selectedData, statusMap]);

  const handleUpdate = async () => {
    const client = selectedData?.client;
    const sysPrinCode = selectedData?.sysPrin;
    if (!client || !sysPrinCode) {
      alert('Missing client or sysPrin.');
      return;
    }

    const url = `http://localhost:8089/client-sysprin-writer/api/sysprins/update/${encodeURIComponent(
      client
    )}/${encodeURIComponent(sysPrinCode)}`;

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

      // Try to read server's canonical copy (optional)
      let saved = null;
      try {
        const ct = res.headers.get('Content-Type') || '';
        if (ct.includes('application/json')) {
          saved = await res.json();
        }
      } catch {}

      // Build the patch this tab owns: statA..statZ
      const patch =
        saved ??
        {
          statA: buildPayload.statA,
          statB: buildPayload.statB,
          statC: buildPayload.statC,
          statD: buildPayload.statD,
          statE: buildPayload.statE,
          statF: buildPayload.statF,
          statI: buildPayload.statI,
          statL: buildPayload.statL,
          statO: buildPayload.statO,
          statU: buildPayload.statU,
          statX: buildPayload.statX,
          statZ: buildPayload.statZ,
        };

      // bubble up so parent/grid stays in sync immediately
      pushGeneralPatch(patch);

      // Optional: clear local overrides after success
      // setStatusMap?.({});

      alert('Statuses updated successfully.');
    } catch (e) {
      console.error(e);
      alert(e?.message || 'Failed to update.');
    } finally {
      setUpdating(false);
    }
  };

  return (
    <CRow className="g-2" style={{ border: '1px solid #ccc', borderRadius: '6px' }}>
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

            {/* Update button */}
            <div className="d-flex justify-content-end mt-2">
              <Button
                variant="contained"
                size="small"
                onClick={handleUpdate}
                disabled={
                  updating || !isEditable || !selectedData?.client || !selectedData?.sysPrin
                }
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





