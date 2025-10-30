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

  // 🔁 Use pushGeneralPatch for any field update
  const updateField = (field) => (value) => pushGeneralPatch({ [field]: value });

  const getvalue = (field, fallback = '') => selectedData?.[field] ?? fallback;

  const custType     = selectedData?.custType || '';
  const returnStatus = selectedData?.returnStatus || '';
  const destroyStatus= selectedData?.destroyStatus || '';
  const special      = selectedData?.special || '';
  const pinMailer    = selectedData?.pinMailer || '';
  const active       = selectedData?.active === true || selectedData?.active === 'Y' ? 'Y' : 'N';
  const rps          = selectedData?.rps === true || selectedData?.rps === 'Y' ? 'Y' : 'N';
  const addrFlag     = selectedData?.addrFlag === true || selectedData?.addrFlag === 'Y' ? 'Y' : 'N';
  const astatRch     = selectedData?.astatRch === true || selectedData?.astatRch === '1' ? '1' : '0';
  const nm13         = selectedData?.nm13 === true || selectedData?.nm13 === '1' ? '1' : '0';

  const handleChange = (field) => (e) => {
    const value = e.target.value;
    pushGeneralPatch({ [field]: value });
  };

  const handleCheckboxChange = (field) => (e) => {
    const checked = e.target.checked;
    let value = '';
    if (['active', 'rps', 'addrFlag'].includes(field)) value = checked ? 'Y' : 'N';
    else if (['astatRch', 'nm13'].includes(field))     value = checked ? '1' : '0';
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
      active: toB(sd.active),
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
                    value={getvalue('contact')}
                    onChange={(e) => updateField('contact')(e.target.value)}
                    InputProps={{ sx: font78 }} disabled={!isEditable}
                  />
                </div>
              </CCol>
              <CCol xs={6}>
                <div style={{ ...rowStyle, height: 'auto' }}>
                  <div style={leftLabel}>Phone</div>
                  <TextField
                    variant="outlined" fullWidth size="small"
                    value={getvalue('phone')}
                    onChange={(e) => updateField('phone')(e.target.value)}
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
                    checked={rps === 'Y'} onChange={handleCheckboxChange('rps')}
                    disabled={!isEditable}
                  />
                </div>
              </CCol>
              <CCol xs={6}>
                <div style={{ ...rowStyle, height: 'auto' }}>
                  <CFormCheck
                    type="checkbox" id="sys-prin-active"
                    label={<span style={labelStyle}>Sys/PRIN Active</span>}
                    checked={active === 'Y'} onChange={handleCheckboxChange('active')}
                    disabled={!isEditable}
                  />
                </div>
              </CCol>
            </CRow>

            <div style={rowStyle} className="mb-3">
              <CFormCheck
                type="checkbox" id="flag-undeliverable"
                label={<span style={labelStyle}>Flag Undeliverable an Invalid Address</span>}
                checked={addrFlag === 'Y'} onChange={handleCheckboxChange('addrFlag')}
                disabled={!isEditable}
              />
            </div>
            <div style={rowStyle} className="mb-3">
              <CFormCheck
                type="checkbox" id="status-research"
                label={<span style={labelStyle}>A Status Accounts Going in Research</span>}
                checked={astatRch === '1'} onChange={handleCheckboxChange('astatRch')}
                disabled={!isEditable}
              />
            </div>
            <div style={rowStyle}>
              <CFormCheck
                type="checkbox" id="perform-non-mon"
                label={<span style={labelStyle}>Perform Non Mon 13 on Destroy</span>}
                checked={nm13 === '1'} onChange={handleCheckboxChange('nm13')}
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




// EditReMailOptions.jsx
import { useEffect, useMemo, useState } from 'react';
import AddIcon from '@mui/icons-material/Add';
import DeleteIcon from '@mui/icons-material/Delete';
import { Box, Button } from '@mui/material';

import {
  CCard, CCardBody, CCol, CRow,
} from '@coreui/react';
import {
  TextField,
  FormControl,
  Select,
  MenuItem,
  Table,
  TableBody,
  TableCell,
  TableContainer,
  TableHead,
  TableRow,
  IconButton,
  Paper
} from '@mui/material';

import '../../../../scss/sys-prin-configuration/client-atm-pin-prefixes.scss';

import {
  unableToDeliver,
  forwardingAddress,
  nonUS,
  invalidState,
  isPOBox,
} from '../utils/FieldValueMapping';

import EditInvalidedAreaWindow from '../utils/EditInvalidedAreaWindow';

 const EditReMailOptions = ({ selectedData, setSelectedData, isEditable, onChangeGeneral }) => {
  const [updating, setUpdating] = useState(false);
   // central helper (same pattern as General)
  const pushGeneralPatch = (patch) => {
    const withKeys = {
      client: selectedData?.client,
      sysPrin: selectedData?.sysPrin,
      ...patch,
    };
    if (typeof onChangeGeneral === 'function') onChangeGeneral(withKeys);
    else setSelectedData((prev) => ({ ...(prev ?? {}), ...withKeys }));
  };

  const getvalue = (field, fallback = '') => selectedData?.[field] ?? fallback;
  const compactCellSx = { py: 0.1, px: 1 };

  const normaliseAreaArray = (arr) =>
    arr.map((area) =>
      typeof area === 'string' ? { area, sysPrin: selectedData?.sysPrin ?? '' } : area
    );

  const getAreaNames = () =>
    getvalue('invalidDelivAreas', []).map((a) => (typeof a === 'string' ? a : a.area));

  const [selectedInvalidAreas, setSelectedInvalidAreas] = useState([]);
  const [openAreaWindow, setOpenAreaWindow] = useState(false);
  const [dialogMode, setDialogMode] = useState('create'); // 'create' | 'delete'
  const [dialogInitialArea, setDialogInitialArea] = useState('');

  useEffect(() => {
    setSelectedInvalidAreas(getAreaNames());
  }, [selectedData?.invalidDelivAreas]);

  const updateField = (field) => (value) => pushGeneralPatch({ [field]: value });

  // Add → open in create mode
  const handleAddArea = () => {
    if (!isEditable) return;
    setDialogMode('create');
    setDialogInitialArea('');
    setOpenAreaWindow(true);
  };

  // Delete → open in delete mode with the clicked area
  const handleOpenDelete = (areaName) => {
    if (!isEditable) return;
    setDialogMode('delete');
    setDialogInitialArea(areaName);
    setOpenAreaWindow(true);
  };

  // Local removal (not used directly because we use dialog)
  const handleDeleteArea = (areaName) => {
    const newNames = selectedInvalidAreas.filter((n) => n !== areaName);
    setSelectedInvalidAreas(newNames);
    updateField('invalidDelivAreas')(normaliseAreaArray(newNames));
  };

  const font78 = { fontSize: '0.73rem' };
  const leftLabel = {
    fontSize: '0.75rem',
    fontWeight: 500,
    minWidth: '160px',
    marginLeft: '2px',
  };

  const hasAreas = selectedInvalidAreas.length > 0;

  // ---------- Build payload (merge page fields with everything else so nothing is lost) ----------
  const buildPayload = useMemo(() => {
    const sd = selectedData ?? {};
    const toBool = (v) => (v === true || v === 'Y'); // backend expects boolean for `active`
    const to10  = (v) => (v === true || v === '1') ? '1' : (v === '0' || v === false ? '0' : (v ?? '0'));
    const toYN  = (v) => (v === true || v === 'Y') ? 'Y' : (v === false || v === 'N' ? 'N' : (v ?? 'N'));

    return {
      // identity
      client: sd.client ?? '',
      sysPrin: sd.sysPrin ?? '',

      // values from THIS page (use the latest values in selectedData)
      holdDays: Number(sd.holdDays ?? 0),
      tempAway: Number(sd.tempAway ?? 0),
      tempAwayAtts: Number(sd.tempAwayAtts ?? 0),
      undeliverable: sd.undeliverable ?? '0',
      forwardingAddress: sd.forwardingAddress ?? '0',
      nonUS: sd.nonUS ?? '0',
      poBox: sd.poBox ?? '0',
      badState: sd.badState ?? '0',
      // NOTE: invalidDelivAreas is managed by its own create/delete APIs; not included in this PUT.

      // everything else — keep unchanged
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
      reportMethod: Number(sd.reportMethod ?? 0),
      session: sd.session ?? '',
      forwardingAddressLine: sd.forwardingAddressLine ?? '', // if your API uses this; else remove
      entityCode: sd.entityCode ?? '0',
      contact: sd.contact ?? '',
      phone: sd.phone ?? '',

      // status letters (unchanged here)
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

    // ✅ define `saved` safely (API may or may not return JSON)
    let saved = null;
    try {
      const ct = res.headers.get('Content-Type') || '';
      if (ct.includes('application/json')) {
        saved = await res.json();
      }
    } catch {}

    // Build the canonical patch for fields this page owns
    const patch = saved ?? {
      holdDays: buildPayload.holdDays,
      tempAway: buildPayload.tempAway,
      tempAwayAtts: buildPayload.tempAwayAtts,
      undeliverable: buildPayload.undeliverable,
      forwardingAddress: buildPayload.forwardingAddress,
      nonUS: buildPayload.nonUS,
      poBox: buildPayload.poBox,
      badState: buildPayload.badState,
      // invalidDelivAreas handled by its own create/delete APIs
    };

    // Bubble up so parent list stays in sync
    pushGeneralPatch(patch);

    alert('Re-mail options updated successfully.');
  } catch (e) {
    console.error(e);
    alert(e?.message || 'Failed to update.');
  } finally {
    setUpdating(false);
  }
};

  return (
    <>
      <CRow className="d-flex justify-content-between align-items-stretch">
        {/* ----------- LEFT column ----------- */}
        <CCol xs={6} className="d-flex justify-content-start">
          <CCard className="mb-0 w-100 d-flex">
            <CCardBody className="d-flex flex-column" style={{ gap: '25px' }}>
              {/* Days to Hold */}
              <div style={{ display: 'flex', alignItems: 'center', gap: '12px' }}>
                <div style={leftLabel}>Days to Hold</div>
                <TextField
                  variant="outlined"
                  fullWidth
                  size="small"
                  value={getvalue('holdDays')}
                  onChange={(e) => updateField('holdDays')(e.target.value)}
                  InputProps={{ sx: font78 }}
                  disabled={!isEditable}
                />
              </div>

              {/* Days to Hold Temp Aways */}
              <div style={{ display: 'flex', alignItems: 'center', gap: '12px' }}>
                <div style={leftLabel}>Days to Hold Temp Aways</div>
                <TextField
                  variant="outlined"
                  fullWidth
                  size="small"
                  value={getvalue('tempAway')}
                  onChange={(e) => updateField('tempAway')(e.target.value)}
                  InputProps={{ sx: font78 }}
                  disabled={!isEditable}
                />
              </div>

              {/* Re-Mail Attempts */}
              <div style={{ display: 'flex', alignItems: 'center', gap: '12px' }}>
                <div style={leftLabel}>Re-Mail Attempts</div>
                <TextField
                  variant="outlined"
                  fullWidth
                  size="small"
                  value={getvalue('tempAwayAtts')}
                  onChange={(e) => updateField('tempAwayAtts')(e.target.value)}
                  InputProps={{ sx: font78 }}
                  disabled={!isEditable}
                />
              </div>

              {/* Unable to Deliver */}
              <div style={{ display: 'flex', alignItems: 'center', gap: '12px' }}>
                <div style={{ ...leftLabel, whiteSpace: 'nowrap' }}>Unable to Deliver</div>
                <FormControl fullWidth size="small" disabled={!isEditable} sx={{ flex: 1 }}>
                  <Select
                    labelId="undeliverable-label"
                    id="undeliverable"
                    value={getvalue('undeliverable')}
                    onChange={(e) => updateField('undeliverable')(e.target.value)}
                    sx={font78}
                  >
                    {unableToDeliver.map((opt) => (
                      <MenuItem key={opt.code} value={opt.code} sx={font78}>
                        {opt.label}
                      </MenuItem>
                    ))}
                  </Select>
                </FormControl>
              </div>

              {/* Forwarding Address */}
              <div style={{ display: 'flex', alignItems: 'center', gap: '12px' }}>
                <div style={{ ...leftLabel, whiteSpace: 'nowrap' }}>Forwarding Address</div>
                <FormControl fullWidth size="small" disabled={!isEditable} sx={{ flex: 1 }}>
                  <Select
                    labelId="forwarding-label"
                    id="forwarding"
                    value={getvalue('forwardingAddress')}
                    onChange={(e) => updateField('forwardingAddress')(e.target.value)}
                    sx={font78}
                  >
                    {forwardingAddress.map((opt) => (
                      <MenuItem key={opt.code} value={opt.code} sx={font78}>
                        {opt.label}
                      </MenuItem>
                    ))}
                  </Select>
                </FormControl>
              </div>
            </CCardBody>
          </CCard>
        </CCol>

        {/* ----------- RIGHT column ----------- */}
        <CCol xs={6} className="d-flex justify-content-end">
          <CCard className="mb-0 w-100" style={{ height: 'auto' }}>
            <CCardBody className="d-flex flex-column gap-3">
              <TableContainer
                component={Paper}
                variant="outlined"
                sx={{
                  height: 120,
                  overflowY: 'auto',
                  scrollbarWidth: 'thin',
                  scrollbarColor: '#cfd8dc #f5f7fa',
                  '&::-webkit-scrollbar': { width: 8, height: 8 },
                  '&::-webkit-scrollbar-track': { backgroundColor: '#f5f7fa', borderRadius: 8 },
                  '&::-webkit-scrollbar-thumb': { backgroundColor: '#cfd8dc', borderRadius: 8, border: '2px solid #f5f7fa' },
                  '&::-webkit-scrollbar-thumb:hover': { backgroundColor: '#bfcbd3' },

                  borderTop: 'none',
                  borderLeft: 'none',
                  borderRight: 'none',

                  position: 'relative',
                }}
              >
                <Table size="small" stickyHeader aria-label="Do Not Deliver table">
                  <TableHead
                    sx={{
                      '& .MuiTableCell-root': {
                        borderTop: 'none',
                        borderLeft: 'none',
                        borderRight: 'none',
                        borderBottom: '1px solid',
                        borderColor: 'divider',
                      },
                    }}
                  >
                    <TableRow sx={{ '& th': compactCellSx }}>
                      <TableCell sx={{ ...compactCellSx, ...font78 }}>
                        <span style={{ color: 'red' }}>Do Not Deliver to ...</span>
                      </TableCell>
                      <TableCell sx={{ ...compactCellSx }} align="right">
                        <IconButton
                          aria-label="Add area"
                          onClick={handleAddArea}
                          disabled={!isEditable}
                          size="small"
                          sx={{
                            width: 22,
                            height: 22,
                            p: 0,
                            border: '1px solid #1976d2',
                            bgcolor: '#fff',
                            color: '#1976d2',
                            borderRadius: '6px',
                            '&:hover': { bgcolor: '#e3f2fd' },
                            '&.Mui-disabled': {
                              borderColor: 'divider',
                              color: 'action.disabled',
                              bgcolor: 'transparent',
                            },
                          }}
                        >
                          <AddIcon sx={{ fontSize: 14 }} />
                        </IconButton>
                      </TableCell>
                    </TableRow>
                  </TableHead>

                  <TableBody>
                    {hasAreas ? (
                      selectedInvalidAreas.map((name, idx) => (
                        <TableRow key={`${name}-${idx}`} sx={{ '& td': compactCellSx }}>
                          <TableCell sx={{ ...compactCellSx, ...font78 }}>{name}</TableCell>
                          <TableCell sx={{ ...compactCellSx }} align="right">
                            <IconButton
                              size="small"
                              aria-label={`Delete ${name}`}
                              onClick={() => handleOpenDelete(name)}  // open delete dialog
                              disabled={!isEditable}
                            >
                              <DeleteIcon fontSize="small" />
                            </IconButton>
                          </TableCell>
                        </TableRow>
                      ))
                    ) : null}
                  </TableBody>
                </Table>

                {!hasAreas && (
                  <Box
                    sx={{
                      position: 'absolute',
                      left: 0,
                      right: 0,
                      top: 36,
                      bottom: 0,
                      display: 'flex',
                      alignItems: 'center',
                      justifyContent: 'center',
                      pointerEvents: 'none',
                      zIndex: 1,
                    }}
                  >
                    <em style={{ fontSize: '0.73rem', color: 'rgba(0,0,0,0.6)' }}>
                      No areas selected.
                    </em>
                  </Box>
                )}
              </TableContainer>

              {/* Non-US */}
              <div style={{ display: 'flex', alignItems: 'center', gap: '12px' }}>
                <div style={{ ...leftLabel, whiteSpace: 'nowrap' }}>Non-US</div>
                <FormControl fullWidth size="small" disabled={!isEditable} sx={{ flex: 1 }}>
                  <Select
                    labelId="nonus-label"
                    id="nonus"
                    value={getvalue('nonUS')}
                    onChange={(e) => updateField('nonUS')(e.target.value)}
                    sx={font78}
                  >
                    {nonUS.map((opt) => (
                      <MenuItem key={opt.code} value={opt.code} sx={font78}>
                        {opt.label}
                      </MenuItem>
                    ))}
                  </Select>
                </FormControl>
              </div>

              {/* Address is P.O. Box */}
              <div style={{ display: 'flex', alignItems: 'center', gap: '12px' }}>
                <div style={{ ...leftLabel, whiteSpace: 'nowrap' }}>Address is P.O. Box</div>
                <FormControl fullWidth size="small" disabled={!isEditable} sx={{ flex: 1 }}>
                  <Select
                    labelId="pobox-label"
                    id="pobox"
                    value={getvalue('poBox')}
                    onChange={(e) => updateField('poBox')(e.target.value)}
                    sx={font78}
                  >
                    {isPOBox.map((opt) => (
                      <MenuItem key={opt.code} value={opt.code} sx={font78}>
                        {opt.label}
                      </MenuItem>
                    ))}
                  </Select>
                </FormControl>
              </div>

              {/* Invalid State */}
              <div style={{ display: 'flex', alignItems: 'center', gap: '12px' }}>
                <div style={{ ...leftLabel, whiteSpace: 'nowrap' }}>Invalid State</div>
                <FormControl fullWidth size="small" disabled={!isEditable} sx={{ flex: 1 }}>
                  <Select
                    labelId="badstate-label"
                    id="badstate"
                    value={getvalue('badState')}
                    onChange={(e) => updateField('badState')(e.target.value)}
                    sx={font78}
                  >
                    {invalidState.map((opt) => (
                      <MenuItem key={opt.code} value={opt.code} sx={font78}>
                        {opt.label}
                      </MenuItem>
                    ))}
                  </Select>
                </FormControl>
              </div>

              {/* ---- Update button ---- */}
              <div className="d-flex justify-content-end mt-1">
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

      {/* --- Add/Delete Invalid Area Dialog --- */}
      <EditInvalidedAreaWindow
        open={openAreaWindow}
        onClose={() => setOpenAreaWindow(false)}
        sysPrin={selectedData?.sysPrin || ''}
        mode={dialogMode}
        initialArea={dialogInitialArea}
        onCreated={(areaCode) => {
          const exists = selectedInvalidAreas.some(
            (n) => String(n).toLowerCase() === String(areaCode).toLowerCase()
          );
          if (exists) return;
          const newNames = [...selectedInvalidAreas, areaCode];
          setSelectedInvalidAreas(newNames);
          const nextArr = normaliseAreaArray(newNames);
          updateField('invalidDelivAreas')(nextArr);     // local + parent
        }}
        onDeleted={(areaCode) => {
          const newNames = selectedInvalidAreas.filter(
            (n) => String(n).toLowerCase() !== String(areaCode).toLowerCase()
          );
          setSelectedInvalidAreas(newNames);
          const nextArr = normaliseAreaArray(newNames);
          updateField('invalidDelivAreas')(nextArr);     // local + parent
        }}
      />
    </>
  );
};

export default EditReMailOptions;


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



const buildPayload = useMemo(() => {
  const sd = selectedData ?? {};

  // If your create API truly needs a space for "none", you can map here:
  const normalizeDestroy = (v) => v == null ? ' ' : v; // or keep as-is if your API accepts '0'

  return {
    // identity
    client:   sd.client || '',
    sysPrin:  sd.sysPrin || '',

    // General tab
    custType:       sd.custType ?? '0',
    returnStatus:   sd.returnStatus ?? '',
    destroyStatus:  normalizeDestroy(sd.destroyStatus ?? '0'),
    special:        sd.special ?? '0',
    pinMailer:      sd.pinMailer ?? '0',
    active:         !!sd.active,                 // boolean
    rps:            String(sd.rps ?? '0'),       // '0' | '1'
    addrFlag:       String(sd.addrFlag ?? '0'),  // '0' | '1'
    astatRch:       String(sd.astatRch ?? '0'),  // '0' | '1'
    nm13:           String(sd.nm13 ?? '0'),      // '0' | '1'
    notes:          sd.notes ?? '',

    // ReMail options tab
    undeliverable:      sd.undeliverable ?? '0',
    poBox:              sd.poBox ?? '0',
    tempAway:           Number(sd.tempAway ?? 0) || 0,
    tempAwayAtts:       Number(sd.tempAwayAtts ?? 0) || 0,
    reportMethod:       Number(sd.reportMethod ?? 0) || 0,
    nonUS:              sd.nonUS ?? '0',
    holdDays:           Number(sd.holdDays ?? 0) || 0,
    forwardingAddress:  sd.forwardingAddress ?? '0',
    contact:            sd.contact ?? '',
    phone:              sd.phone ?? '',
    entityCode:         sd.entityCode ?? '0',
    session:            sd.session ?? '',
    badState:           sd.badState ?? '0',
    // (invalidDelivAreas is managed by its own APIs, so usually omitted here)

    // Status options tab
    statA: String(sd.statA ?? '0'),
    statB: String(sd.statB ?? '0'),
    statC: String(sd.statC ?? '0'),
    statD: String(sd.statD ?? '0'),
    statE: String(sd.statE ?? '0'),
    statF: String(sd.statF ?? '0'),
    statI: String(sd.statI ?? '0'),
    statL: String(sd.statL ?? '0'),
    statO: String(sd.statO ?? '0'),
    statU: String(sd.statU ?? '0'),
    statX: String(sd.statX ?? '0'),
    statZ: String(sd.statZ ?? '0'),
  };
}, [selectedData]);







