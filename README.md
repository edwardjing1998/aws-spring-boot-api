import React, { useState, useEffect } from 'react';
import { Box, IconButton, Tabs, Tab, Button } from '@mui/material';
import CloseIcon from '@mui/icons-material/Close';
import { CRow, CCol } from '@coreui/react';

import EditSysPrinGeneral   from '../sys-prin-config/EditSysPrinGeneral';
import EditReMailOptions    from '../sys-prin-config/EditReMailOptions';
import EditStatusOptions    from '../sys-prin-config/EditStatusOptions';
import EditFileReceivedFrom from '../sys-prin-config/EditFileReceivedFrom';
import EditFileSentTo       from '../sys-prin-config/EditFileSentTo';
import EditSysPrinNotes     from '../sys-prin-config/EditSysPrinNotes';

const titleByMode = {
  new: 'New Sys/Prin',
  edit: 'Edit Sys/Prin',
  duplicate: 'Duplicate Sys/Prin',
  move: 'Move Sys/Prin',
  changeAll: 'Change Sys/Prin',
};

const SysPrinInformationWindow = ({
  onClose,
  mode,
  selectedData,
  setSelectedData,
  selectedGroupRow,
  // NEW: focused updaters from parent
  onChangeVendorReceivedFrom,
  onChangeVendorSentTo,
}) => {
  const [tabIndex, setTabIndex] = useState(0);
  const [isEditable, setIsEditable] = useState(true);
  const [statusMap, setStatusMap] = useState({});

  useEffect(() => {
    setIsEditable(mode === 'edit' || mode === 'new');
  }, [mode]);

  const maxTabs = 6;
  const nextTab = () => setTabIndex(i => Math.min(i + 1, maxTabs - 1));
  const prevTab = () => setTabIndex(i => Math.max(i - 1, 0));

  const primaryLabel =
    mode === 'changeAll' ? 'Change All' :
    mode === 'duplicate' ? 'Duplicate' :
    mode === 'move' ? 'Move' :
    'Save';

  return (
    <Box sx={{ p: 2, height: '100%' }}>
      {/* Header */}
      <Box
        sx={{
          display: 'flex', justifyContent: 'space-between', alignItems: 'center',
          bgcolor: '#1976d2', color: '#fff', px: 2, py: 1, mx: -4, mt: -4, borderRadius: 0,
        }}
      >
        <span style={{ fontSize: '0.9rem', fontWeight: 500 }}>
          {titleByMode[mode] ?? 'Sys/Prin'}
        </span>
        <IconButton onClick={onClose} size="small" sx={{ color: '#fff' }}>
          <CloseIcon fontSize="small" />
        </IconButton>
      </Box>

      <Box
        sx={{
          display: 'flex', justifyContent: 'space-between', alignItems: 'center',
          mt: '20px', backgroundColor: '#f3f6f8', height: '50px', width: '100%', px: 2, gap: 2,
        }}
      >
        <input
          type="text"
          placeholder="Enter Sys/Prin Name"
          value={selectedData?.sysPrin || ''}
          onChange={(e) => setSelectedData((prev) => ({ ...prev, sysPrin: e.target.value }))}
          disabled={!isEditable}
          style={{
            fontSize: '0.9rem', fontWeight: 400, width: '30vw', height: '30px',
            border: '1px solid #ccc', borderRadius: '4px', paddingLeft: '8px', backgroundColor: 'white',
          }}
        />
      </Box>

      <Tabs
        value={tabIndex}
        onChange={(_, v) => setTabIndex(v)}
        variant="scrollable"
        scrollButtons="auto"
        sx={{ mt: 1, mb: 2 }}
      >
        <Tab label="General"        sx={{ textTransform: 'none', minWidth: 120, fontSize: '0.78rem' }} />
        <Tab label="Remail Options" sx={{ textTransform: 'none', minWidth: 160, fontSize: '0.78rem' }} />
        <Tab label="Status Options" sx={{ textTransform: 'none', minWidth: 160, fontSize: '0.78rem' }} />
        <Tab label="File Received From" sx={{ textTransform: 'none', minWidth: 160, fontSize: '0.78rem' }} />
        <Tab label="File Sent To"       sx={{ textTransform: 'none', minWidth: 160, fontSize: '0.78rem' }} />
        <Tab label="SysPrin Note"       sx={{ textTransform: 'none', minWidth: 160, fontSize: '0.78rem' }} />
      </Tabs>

      <Box sx={{ minHeight: '400px', mt: 2 }}>
        {tabIndex === 0 && (
          <EditSysPrinGeneral
            selectedData={selectedData}
            setSelectedData={setSelectedData}
            isEditable={isEditable}
          />
        )}

        {tabIndex === 1 && (
          <EditReMailOptions
            selectedData={selectedData}
            setSelectedData={setSelectedData}
            isEditable={isEditable}
          />
        )}

        {tabIndex === 2 && (
          <EditStatusOptions
            selectedData={selectedData}
            statusMap={statusMap}
            setStatusMap={setStatusMap}
            isEditable={isEditable}
          />
        )}

        {tabIndex === 3 && (
          <EditFileReceivedFrom
            key={`received-from-${selectedData?.sysPrin ?? ''}`}
            selectedData={selectedData}
            isEditable={isEditable}
            onChangeVendorReceivedFrom={onChangeVendorReceivedFrom} // <-- forward focused updater
            setSelectedData={setSelectedData} // optional fallback
          />
        )}

        {tabIndex === 4 && (
          <EditFileSentTo
            key={`sent-to-${selectedData?.sysPrin ?? ''}`}
            selectedData={selectedData}
            isEditable={isEditable}
            onChangeVendorSentTo={onChangeVendorSentTo} // <-- forward focused updater
            setSelectedData={setSelectedData} // optional fallback
          />
        )}

        {tabIndex === 5 && (
          <EditSysPrinNotes
            selectedData={selectedData}
            setSelectedData={setSelectedData}
            isEditable={isEditable}
          />
        )}
      </Box>

      <CRow className="mt-3">
        <CCol style={{ display: 'flex', justifyContent: 'flex-start' }}>
          <Button variant="outlined" size="small" onClick={() => setTabIndex(i => Math.max(i - 1, 0))}>
            Back
          </Button>
        </CCol>

        <CCol style={{ display: 'flex', justifyContent: 'flex-end', gap: '12px' }}>
          <Button variant="contained" size="small">
            {primaryLabel}
          </Button>
          <Button variant="outlined" size="small" onClick={() => setTabIndex(i => Math.min(i + 1, 5))}>
            Next
          </Button>
        </CCol>
      </CRow>
    </Box>
  );
};

export default SysPrinInformationWindow;


import React, { useMemo, useState } from 'react';
import { 
  CCard, 
  CCardBody, 
  CCol, 
  CRow, 
  CFormCheck 
} from '@coreui/react';

import TextField from '@mui/material/TextField';
import Select from '@mui/material/Select';
import MenuItem from '@mui/material/MenuItem';
import FormControl from '@mui/material/FormControl';
import Button from '@mui/material/Button';

import '../../../../scss/sys-prin-configuration/client-atm-pin-prefixes.scss';

const rowStyle = {
  display: 'flex',
  alignItems: 'center',
  height: '50px',
};

const font78 = { fontSize: '0.78rem' };
const labelStyle = { margin: 0, fontSize: '0.78rem' };

const SysPrinGeneral = ({ selectedData, setSelectedData, isEditable }) => {
  const [updating, setUpdating] = useState(false);

  const updateField = (field) => (value) =>
    setSelectedData((prev) => ({ ...prev, [field]: value }));

  const getvalue = (field, fallback = '') => selectedData?.[field] ?? fallback;

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
    minWidth: '60px',
    marginLeft: '2px',
  };

  // Build request body from selectedData (keep untouched values as-is)
  const buildPayload = useMemo(() => {
    const sd = selectedData ?? {};
    const toBool = (v) => (v === true || v === 'Y');
    const to10  = (v) => (v === true || v === '1') ? '1' : (v === '0' || v === false ? '0' : (v ?? '0'));
    const toYN  = (v) => (v === true || v === 'Y') ? 'Y' : (v === false || v === 'N' ? 'N' : (v ?? 'N'));

    return {
      client: sd.client ?? '',
      sysPrin: sd.sysPrin ?? '',

      // dropdowns on this page
      custType: sd.custType ?? '0',
      returnStatus: sd.returnStatus ?? '',
      destroyStatus: sd.destroyStatus ?? '0',
      special: sd.special ?? '0',
      pinMailer: sd.pinMailer ?? '0',

      // flags/booleans
      active: toBool(sd.active),      // API example uses boolean
      rps: toYN(sd.rps),              // keep as Y/N if your API expects that; change to '0'/'1' if needed
      addrFlag: toYN(sd.addrFlag),
      astatRch: to10(sd.astatRch),
      nm13: to10(sd.nm13),

      // text/number fields (keep whatever we already have)
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

      // status letters (keep existing)
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

    // Try to read back server-normalized payload; if none, fall back to what we sent
    let saved = null;
    try { saved = await res.json(); } catch {}

    // ✅ Merge into parent selectedData (mirrors your EditFileSentTo push)
    setSelectedData(prev => ({
      ...(prev ?? {}),
      ...(saved || buildPayload),
    }));

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

            {/* Update button (left column) */}
            <div style={{ display: 'flex', justifyContent: 'flex-end', marginTop: 8 }}>
               &nbsp;
            </div>
          </CCardBody>
        </CCard>
      </CCol>

      <CCol xs={6}>
        <CCard className="mb-4">
          <CCardBody>
            {/* Contact / Phone row */}
            <CRow className="mb-3 align-items-center">
              <CCol xs={6}>
                <div style={{ ...rowStyle, height: 'auto' }}>
                  <div style={leftLabel}>Contact</div>
                  <TextField
                    variant="outlined"
                    fullWidth
                    size="small"
                    value={getvalue('contact')}
                    onChange={(e) => updateField('contact')(e.target.value)}
                    InputProps={{ sx: font78 }}
                    disabled={!isEditable}
                  />
                </div>
              </CCol>
              <CCol xs={6}>
                <div style={{ ...rowStyle, height: 'auto' }}>
                  <div style={leftLabel}>Phone</div>
                  <TextField
                    variant="outlined"
                    fullWidth
                    size="small"
                    value={getvalue('phone')}
                    onChange={(e) => updateField('phone')(e.target.value)}
                    InputProps={{ sx: font78 }}
                    disabled={!isEditable}
                  />
                </div>
              </CCol>
            </CRow>

            {/* RPS Customer + Active */}
            <CRow className="mb-3 align-items-center">
              <CCol xs={6}>
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
              <CCol xs={6}>
                <div style={{ ...rowStyle, height: 'auto' }}>
                  <CFormCheck
                    type="checkbox"
                    id="sys-prin-active"
                    label={<span style={labelStyle}>Sys/PRIN Active</span>}
                    checked={active === 'Y'}
                    onChange={handleCheckboxChange('active')}
                    disabled={!isEditable}
                  />
                </div>
              </CCol>
            </CRow>

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
    </CRow>
  );
};

export default SysPrinGeneral;

