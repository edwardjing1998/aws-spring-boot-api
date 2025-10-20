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
  changeAll: 'Change Sys/Prin', // for "Change All"
};

const SysPrinInformationWindow = ({ onClose,
  mode,
  selectedData,
  setSelectedData,
  selectedGroupRow
}) => {

  const [tabIndex, setTabIndex] = useState(0);
  const [isEditable, setIsEditable] = useState(true);
  const [statusMap, setStatusMap] = useState({});

  // extra fields for Duplicate/Move/Change All (minimal UI)
  const [targetSysPrin, setTargetSysPrin] = useState('');
  const [newClient, setNewClient] = useState('');
  const [copyAreas, setCopyAreas] = useState(true); // for duplicate/changeAll if needed

  /* enable / disable fields based on mode (keep minimal change) */
  useEffect(() => {
    setIsEditable(mode === 'edit' || mode === 'new');
  }, [mode]);

  /* tabs */
  const maxTabs = 6;
  const nextTab = () => setTabIndex(i => Math.min(i + 1, maxTabs - 1));
  const prevTab = () => setTabIndex(i => Math.max(i - 1, 0));

  /* SAVE (existing edit/new logic kept intact) */
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

  /* NEW: Primary action for changeAll/duplicate/move */
  const handlePrimaryAction = async () => {
    const client = (selectedGroupRow?.client ?? '').trim();
    const currentSysPrin = (selectedData?.sysPrin ?? '').trim();

    try {
      if (mode === 'changeAll') {
        // Uses your earlier endpoint pattern:
        // POST /api/clients/{clientId}/sysprins/copy-from/{templateSysPrin}
        if (!client) { alert('Client is required.'); return; }
        if (!currentSysPrin) { alert('Template Sys/Prin is required.'); return; }

        const url = `http://localhost:8084/sysprin-service/api/clients/${encodeURIComponent(client)}/sysprins/copy-from/${encodeURIComponent(currentSysPrin)}`;
        const res = await fetch(url, { method: 'POST' });
        if (!res.ok) {
          const text = await res.text().catch(() => '');
          throw new Error(`Change All failed (HTTP ${res.status}). ${text}`);
        }
        alert('Change All completed successfully.');
        return;
      }

      if (mode === 'duplicate') {
        // Example endpoint (adjust to your controller path if different):
        // POST /api/sysprins/{client}/{source}/duplicate/{target}?copyAreas=true|false
        if (!client) { alert('Client is required.'); return; }
        if (!currentSysPrin) { alert('Source Sys/Prin is required.'); return; }
        if (!targetSysPrin.trim()) { alert('Target Sys/Prin is required.'); return; }

        const url = `http://localhost:8084/sysprin-service/api/sysprins/${encodeURIComponent(client)}/${encodeURIComponent(currentSysPrin)}/duplicate/${encodeURIComponent(targetSysPrin.trim())}?copyAreas=${copyAreas}`;
        const res = await fetch(url, { method: 'POST' });
        if (!res.ok) {
          const text = await res.text().catch(() => '');
          throw new Error(`Duplicate failed (HTTP ${res.status}). ${text}`);
        }
        alert('Duplicate completed successfully.');
        return;
      }

      if (mode === 'move') {
        // Example endpoint (adjust to your controller path if different):
        // PUT /api/sysprins/{oldClient}/{sysPrin}/move/{newClient}
        if (!currentSysPrin) { alert('Sys/Prin is required.'); return; }
        if (!client) { alert('Old Client is required.'); return; }
        if (!newClient.trim()) { alert('New Client is required.'); return; }

        const url = `http://localhost:8084/sysprin-service/api/sysprins/${encodeURIComponent(client)}/${encodeURIComponent(currentSysPrin)}/move/${encodeURIComponent(newClient.trim())}`;
        const res = await fetch(url, { method: 'PUT' });
        if (!res.ok) {
          const text = await res.text().catch(() => '');
          throw new Error(`Move failed (HTTP ${res.status}). ${text}`);
        }
        alert('Move completed successfully.');
        return;
      }
    } catch (err) {
      console.error('Operation error:', err);
      alert(err?.message ?? String(err));
    }
  };

  // Primary button label per mode (kept Save for edit/new)
  const primaryLabel =
    mode === 'changeAll' ? 'Apply' :
    mode === 'duplicate' ? 'Duplicate' :
    mode === 'move' ? 'Move' :
    'Save';

  return (
    <Box sx={{ p: 2, height: '100%' }}>
      {/* Blue header + dynamic title */}
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

      {/* Name row */}
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
          gap: 2,
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

        {/* Minimal mode-specific inputs */}
        {mode === 'duplicate' && (
          <div style={{ display: 'flex', alignItems: 'center', gap: 8 }}>
            <label style={{ fontSize: '0.85rem' }}>Target Sys/Prin:</label>
            <input
              type="text"
              value={targetSysPrin}
              onChange={(e) => setTargetSysPrin(e.target.value)}
              placeholder="New Sys/Prin"
              style={{
                fontSize: '0.9rem',
                height: '30px',
                border: '1px solid #ccc',
                borderRadius: '4px',
                paddingLeft: '8px',
                backgroundColor: 'white',
              }}
            />
            <label style={{ fontSize: '0.85rem', marginLeft: 8 }}>
              <input
                type="checkbox"
                checked={copyAreas}
                onChange={(e) => setCopyAreas(e.target.checked)}
                style={{ marginRight: 4 }}
              />
              Copy Areas
            </label>
          </div>
        )}

        {mode === 'move' && (
          <div style={{ display: 'flex', alignItems: 'center', gap: 8 }}>
            <span style={{ fontSize: '0.85rem' }}>Old Client: <b>{(selectedGroupRow?.client ?? '').trim() || '(n/a)'}</b></span>
            <label style={{ fontSize: '0.85rem' }}>New Client:</label>
            <input
              type="text"
              value={newClient}
              onChange={(e) => setNewClient(e.target.value)}
              placeholder="New Client ID"
              style={{
                fontSize: '0.9rem',
                height: '30px',
                border: '1px solid #ccc',
                borderRadius: '4px',
                paddingLeft: '8px',
                backgroundColor: 'white',
              }}
            />
          </div>
        )}

        {mode === 'changeAll' && (
          <div style={{ display: 'flex', alignItems: 'center', gap: 8 }}>
            <span style={{ fontSize: '0.85rem' }}>Client: <b>{(selectedGroupRow?.client ?? '').trim() || '(n/a)'}</b></span>
            <span style={{ fontSize: '0.85rem' }}>
              Template: <b>{(selectedData?.sysPrin ?? '').trim() || '(set Sys/Prin above)'}</b>
            </span>
            <label style={{ fontSize: '0.85rem', marginLeft: 8 }}>
              <input
                type="checkbox"
                checked={copyAreas}
                onChange={(e) => setCopyAreas(e.target.checked)}
                style={{ marginRight: 4 }}
              />
              Copy Areas
            </label>
          </div>
        )}
      </Box>

      <Tabs
        value={tabIndex}
        onChange={(_, v) => setTabIndex(v)}
        variant="scrollable"
        scrollButtons="auto"
        sx={{ mt: 1, mb: 2 }}
      >
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
            selectedData={selectedData}
            setSelectedData={setSelectedData}
            isEditable={isEditable}
          />
        )}

        {tabIndex === 4 && (
          <EditFileSentTo
            selectedData={selectedData}
            setSelectedData={setSelectedData}
            isEditable={isEditable}
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

      {/* navigation + primary action */}
      <CRow className="mt-3">
        <CCol style={{ display: 'flex', justifyContent: 'flex-start' }}>
          <Button
            variant="outlined"
            size="small"
            onClick={prevTab}
            disabled={tabIndex === 0}
          >
            Back
          </Button>
        </CCol>

        <CCol style={{ display: 'flex', justifyContent: 'flex-end', gap: '12px' }}>
          {/* For edit/new, Save keeps your original behavior.
              For changeAll/duplicate/move, call the new primary action. */}
          {mode === 'edit' || mode === 'new' ? (
            <Button
              variant="contained"
              size="small"
              onClick={handleSave}
              disabled={!isEditable}
            >
              Save
            </Button>
          ) : (
            <Button
              variant="contained"
              size="small"
              onClick={handlePrimaryAction}
            >
              {primaryLabel}
            </Button>
          )}

          <Button
            variant="outlined"
            size="small"
            onClick={nextTab}
            disabled={tabIndex === maxTabs - 1}
          >
            Next
          </Button>
        </CCol>
      </CRow>
    </Box>
  );
};

export default SysPrinInformationWindow;

<Box sx={{ px: 2, pb: 2, pt: 0, height: '100%' }}>





