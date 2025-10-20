import React from 'react';
import { CCard, CCardBody } from '@coreui/react';
import { Button } from '@mui/material';

const SysPrinEditButtonPanel = ({ setSysPrinInformationWindow }) => {
  return (
    <CCard style={{ height: '35px', marginBottom: '4px', marginTop: '25px' }}>
      <CCardBody
        className="d-flex align-items-center"
        style={{ padding: '0.25rem 0.5rem', height: '100%' }}
      >
        <div>
          <Button
            variant="outlined"
            onClick={() => setSysPrinInformationWindow({ open: true, mode: 'change' })}
            size="small"
            sx={{ fontSize: '0.78rem', marginRight: '6px', textTransform: 'none' }}
          >
            Change All
          </Button>

          <Button
            variant="outlined"
            onClick={() => setSysPrinInformationWindow({ open: true, mode: 'edit' })}
            size="small"
            sx={{ fontSize: '0.78rem', textTransform: 'none', marginRight: '6px' }}
          >
            Edit SysPrin
          </Button>

          <Button
            variant="outlined"
            onClick={() => setSysPrinInformationWindow({ open: true, mode: 'new' })}
            size="small"
            sx={{ fontSize: '0.78rem', marginRight: '6px', textTransform: 'none' }}
          >
            New SysPrin
          </Button>

          <Button
            variant="outlined"
            onClick={() => setSysPrinInformationWindow({ open: true, mode: 'duplicate' })}
            size="small"
            sx={{ fontSize: '0.78rem', marginRight: '6px', textTransform: 'none' }}
          >
            Duplicate
          </Button>

          <Button
            variant="outlined"
            onClick={() => setSysPrinInformationWindow({ open: true, mode: 'move' })}
            size="small"
            sx={{ fontSize: '0.78rem', marginRight: '6px', textTransform: 'none' }}
          >
            Move
          </Button>
        </div>
      </CCardBody>
    </CCard>
  );
};

export default SysPrinEditButtonPanel;




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
  change: 'Change Sys/Prin', // for "Change All" button
};

const SysPrinInformationWindow = ({ onClose, mode, selectedData, setSelectedData, selectedGroupRow }) => {
  const [tabIndex, setTabIndex] = useState(0);
  const [isEditable, setIsEditable] = useState(true);
  const [statusMap, setStatusMap] = useState({});

  useEffect(() => {
    setIsEditable(mode === 'edit' || mode === 'new'); // keep your original intent
  }, [mode]);

  const maxTabs = 6;
  const nextTab = () => setTabIndex(i => Math.min(i + 1, maxTabs - 1));
  const prevTab = () => setTabIndex(i => Math.max(i - 1, 0));

  const handleSave = async () => {
    try {
      const client = (selectedGroupRow?.client ?? '').trim();
      const sysPrin = (selectedData?.sysPrin ?? '').trim();
      const data = { ...selectedData, ...statusMap };

      if (!client) { alert('Client is required before saving.'); return; }
      if (!sysPrin) { alert('Sys/Prin is required before saving.'); return; }

      const payload = {
        client, sysPrin,
        custType: data?.custType ?? '',
        undeliverable: data?.undeliverable ?? '',
        statA: data?.statA ?? '',
        statB: data?.statB ?? '',
        statC: data?.statC ?? '',
        statD: data?.statD ?? '',
        statE: data?.statE ?? '',
        statF: data?.statF ?? '',
        statI: data?.statI ?? '',
        statL: data?.statL ?? '',
        statO: data?.statO ?? '',
        statU: data?.statU ?? '',
        statX: data?.statX ?? '',
        statZ: data?.statZ ?? '',
        poBox: data?.poBox ?? '',
        addrFlag: data?.addrFlag ?? '',
        tempAway: Number(data?.tempAway ?? 0),
        rps: data?.rps ?? '',
        session: data?.session ?? '',
        badState: data?.badState ?? '',
        astatRch: data?.astatRch ?? '',
        nm13: data?.nm13 ?? '',
        tempAwayAtts: Number(data?.tempAwayAtts ?? 0),
        reportMethod: Number(data?.reportMethod ?? 0),
        active: Number(data?.active ?? 0),
        notes: data?.notes ?? '',
        returnStatus: data?.returnStatus ?? '',
        destroyStatus: data?.destroyStatus ?? '',
        nonUS: data?.nonUS ?? '',
        special: data?.special ?? '',
        pinMailer: data?.pinMailer ?? '',
        holdDays: Number(data?.holdDays ?? 0),
        forwardingAddress: data?.forwardingAddress ?? '',
        contact: data?.contact ?? '',
        phone: data?.phone ?? '',
        entityCode: data?.entityCode ?? '',
      };

      const url = `http://localhost:8084/sysprin-service/api/sysprins/${encodeURIComponent(client)}/${encodeURIComponent(sysPrin)}`;
      const res = await fetch(url, { method: 'POST', headers: { 'Content-Type': 'application/json' }, body: JSON.stringify(payload) });
      if (!res.ok) {
        const text = await res.text().catch(() => '');
        throw new Error(`Save failed (HTTP ${res.status}). ${text}`);
      }
      const saved = await res.json().catch(() => null);
      alert('Saved successfully.');
      if (saved && typeof saved === 'object') setSelectedData(prev => ({ ...prev, ...saved }));
    } catch (err) {
      console.error('Save error:', err);
      alert(`Save error: ${err.message ?? err}`);
    }
  };

  return (
    <Box sx={{ p: 2, height: '100%' }}>
      {/* Blue header with dynamic title text */}
      <Box
        sx={{
          display: 'flex',
          justifyContent: 'space-between',
          alignItems: 'center',
          mt: '-25px',
          bgcolor: '#1976d2',
          color: '#fff',
          px: 2,
          py: 1,
          borderRadius: '6px',
        }}
      >
        <span style={{ fontSize: '0.9rem', fontWeight: 500 }}>
          {titleByMode[mode] ?? 'Sys/Prin'}
        </span>
        <IconButton onClick={onClose} size="small" sx={{ color: '#fff' }}>
          <CloseIcon fontSize="small" />
        </IconButton>
      </Box>

      {/* (rest unchanged) */}
      <Box
        sx={{
          display: 'flex',
          justifyContent: 'space-between',
          alignItems: 'center',
          mt: '20px',
          backgroundColor: '#f3f6f8',
          height: '50px',
          width: '100%',
          px: 2,
        }}
      >
        <input
          type="text"
          placeholder="Enter Sys/Prin Name"
          value={selectedData?.sysPrin || ''}
          onChange={(e) =>
            setSelectedData((prev) => ({
              ...prev,
              sysPrin: e.target.value,
            }))
          }
          disabled={!isEditable}
          style={{
            fontSize: '0.9rem',
            fontWeight: 400,
            width: '30vw',
            height: '30px',
            border: '1px solid #ccc',
            borderRadius: '4px',
            paddingLeft: '8px',
            backgroundColor: 'white',
          }}
        />
      </Box>

      <Tabs value={tabIndex} onChange={(_, v) => setTabIndex(v)} variant="scrollable" scrollButtons="auto" sx={{ mt: 1, mb: 2 }}>
        <Tab label={<Box sx={{ display: 'flex', alignItems: 'center', gap: 1 }}>
            <Box sx={{ width: 20, height: 20, borderRadius: '50%', backgroundColor: '#1976d2', color: 'white', fontSize: '0.75rem', display: 'flex', alignItems: 'center', justifyContent: 'center' }}>1</Box>
            General
          </Box>}
          sx={{ textTransform: 'none', minWidth: 120, fontSize: '0.78rem' }}
        />
        <Tab label={<Box sx={{ display: 'flex', alignItems: 'center', gap: 1 }}>
            <Box sx={{ width: 20, height: 20, borderRadius: '50%', backgroundColor: '#1976d2', color: 'white', fontSize: '0.75rem', display: 'flex', alignItems: 'center', justifyContent: 'center' }}>2</Box>
            Remail Options
          </Box>}
          sx={{ textTransform: 'none', minWidth: 160, fontSize: '0.78rem' }}
        />
        <Tab label={<Box sx={{ display: 'flex', alignItems: 'center', gap: 1 }}>
            <Box sx={{ width: 20, height: 20, borderRadius: '50%', backgroundColor: '#1976d2', color: 'white', fontSize: '0.75rem', display: 'flex', alignItems: 'center', justifyContent: 'center' }}>3</Box>
            Status Options
          </Box>}
          sx={{ textTransform: 'none', minWidth: 160, fontSize: '0.78rem' }}
        />
        <Tab label={<Box sx={{ display: 'flex', alignItems: 'center', gap: 1 }}>
            <Box sx={{ width: 20, height: 20, borderRadius: '50%', backgroundColor: '#1976d2', color: 'white', fontSize: '0.75rem', display: 'flex', alignItems: 'center', justifyContent: 'center' }}>4</Box>
            File Received From
          </Box>}
          sx={{ textTransform: 'none', minWidth: 160, fontSize: '0.78rem' }}
        />
        <Tab label={<Box sx={{ display: 'flex', alignItems: 'center', gap: 1 }}>
            <Box sx={{ width: 20, height: 20, borderRadius: '50%', backgroundColor: '#1976d2', color: 'white', fontSize: '0.75rem', display: 'flex', alignItems: 'center', justifyContent: 'center' }}>5</Box>
            File Sent To
          </Box>}
          sx={{ textTransform: 'none', minWidth: 160, fontSize: '0.78rem' }}
        />
        <Tab label={<Box sx={{ display: 'flex', alignItems: 'center', gap: 1 }}>
            <Box sx={{ width: 20, height: 20, borderRadius: '50%', backgroundColor: '#1976d2', color: 'white', fontSize: '0.75rem', display: 'flex', alignItems: 'center', justifyContent: 'center' }}>6</Box>
            SysPrin Note
          </Box>}
          sx={{ textTransform: 'none', minWidth: 160, fontSize: '0.78rem' }}
        />
      </Tabs>

      <Box sx={{ minHeight: '400px', mt: 2 }}>
        {tabIndex === 0 && <EditSysPrinGeneral selectedData={selectedData} setSelectedData={setSelectedData} isEditable={isEditable} />}
        {tabIndex === 1 && <EditReMailOptions selectedData={selectedData} setSelectedData={setSelectedData} isEditable={isEditable} />}
        {tabIndex === 2 && <EditStatusOptions selectedData={selectedData} statusMap={statusMap} setStatusMap={setStatusMap} isEditable={isEditable} />}
        {tabIndex === 3 && <EditFileReceivedFrom selectedData={selectedData} setSelectedData={setSelectedData} isEditable={isEditable} />}
        {tabIndex === 4 && <EditFileSentTo selectedData={selectedData} setSelectedData={setSelectedData} isEditable={isEditable} />}
        {tabIndex === 5 && <EditSysPrinNotes selectedData={selectedData} setSelectedData={setSelectedData} isEditable={isEditable} />}
      </Box>

      <CRow className="mt-3">
        <CCol style={{ display: 'flex', justifyContent: 'flex-start' }}>
          <Button variant="outlined" size="small" onClick={prevTab} disabled={tabIndex === 0}>Back</Button>
        </CCol>
        <CCol style={{ display: 'flex', justifyContent: 'flex-end', gap: '12px' }}>
          <Button variant="contained" size="small" onClick={handleSave} disabled={!isEditable}>Save</Button>
          <Button variant="outlined" size="small" onClick={nextTab} disabled={tabIndex === maxTabs - 1}>Next</Button>
        </CCol>
      </CRow>
    </Box>
  );
};

export default SysPrinInformationWindow;


