import React, { useState, useEffect } from 'react';
import { Box, IconButton, Tabs, Tab, Button, TextField, Snackbar, Alert } from '@mui/material';
import CloseIcon from '@mui/icons-material/Close';
import { CRow, CCol } from '@coreui/react';

import EditClientInformation from '../EditClientInformation';
import EditAtmCashPrefix from '../EditAtmCashPrefix';
import EditClientReport from '../EditClientReport';
import EditClientEmailSetup from '../EditClientEmailSetup';

import { handleCreate, handleUpdate, handleDelete } from './ClientService';

const ClientInformationWindow = ({
  onClose,
  selectedGroupRow,
  setSelectedGroupRow,
  mode,
  // (optional) already in your previous flow
  onClientCreated,
  onClientUpdated,
  // NEW: bubble email changes up to the page
  onClientEmailsChanged,
}) => {
  const [tabIndex, setTabIndex] = useState(0);
  const [isEditable, setIsEditable] = useState(true);
  const [status, setStatus] = useState({ open: false, severity: 'info', message: '' });

  const makeEmptyClient = () => ({
    client: '', name: '', addr: '', city: '', state: '', zip: '',
    contact: '', phone: '', active: true, faxNumber: '', billingSp: '',
    reportBreakFlag: 0, chLookUpType: 0, excludeFromReport: false, positiveReports: false,
    subClientInd: false, subClientXref: '', amexIssued: false,
    clientEmail: [], // helpful default
  });

  useEffect(() => {
    if (mode === 'new') {
      setIsEditable(true);
      setSelectedGroupRow(prev => (prev && Object.keys(prev).length ? prev : makeEmptyClient()));
    } else if (mode === 'edit') {
      setIsEditable(true);
    } else {
      setIsEditable(false);
    }
  }, [mode, setSelectedGroupRow]);

  const viewRow = mode === 'new'
    ? (selectedGroupRow ?? makeEmptyClient())
    : (selectedGroupRow ?? makeEmptyClient());

  const sharedSx = {
    '& .MuiInputBase-root': { height: '30px', fontSize: '0.78rem' },
    '& .MuiInputBase-input': { padding: '4px 4px', height: '30px', fontSize: '0.78rem', lineHeight: '1rem' },
    '& .MuiInputLabel-root': { fontSize: '0.78rem', lineHeight: '1rem' },
    '& .MuiInputBase-input.Mui-disabled': { color: 'black', WebkitTextFillColor: 'black' },
    '& .MuiInputLabel-root.Mui-disabled': { color: 'black' },
  };

  const showStatus = (message, severity = 'success') =>
    setStatus({ open: true, severity, message });
  const handleStatusClose = (_e, reason) => {
    if (reason === 'clickaway') return;
    setStatus(prev => ({ ...prev, open: false }));
  };

  const onCreateClick = async () => {
    try {
      const saved = await handleCreate(viewRow);
      showStatus('Client created successfully', 'success');
      setSelectedGroupRow(prev => ({ ...(prev ?? {}), ...(saved ?? {}) }));
      onClientCreated?.(saved);
    } catch (err) {
      console.error(err);
      showStatus(err.message || 'An error occurred while creating.', 'error');
    }
  };

  const onUpdateClick = async () => {
    try {
      const raw = await handleUpdate(viewRow);

      // ✅ Normalize: if server returns text or a "thin" object, use what the user just edited.
      const saved =
        raw && typeof raw === 'object'
          ? { ...viewRow, ...raw } // enrich "thin" payload with the current form values
          : { ...viewRow, _message: typeof raw === 'string' ? raw : undefined };

      showStatus('Client updated successfully', 'success');

      // ✅ Update the modal’s local view immediately
      setSelectedGroupRow(prev => ({ ...(prev ?? {}), ...(saved ?? {}) }));

      // ✅ Bubble to the page so the left list & preview update
      onClientUpdated?.(saved);
    } catch (err) {
      console.error(err);
      showStatus(err.message || 'An error occurred while updating.', 'error');
    }
  };

  const onDeleteClick = async () => {
    if (!viewRow?.client) {
      showStatus('Client ID is required to delete.', 'warning');
      return;
    }
    if (!window.confirm(`Delete client "${viewRow.client}"? This cannot be undone.`)) return;
    try {
      await handleDelete(viewRow.client);
      showStatus('Client deleted successfully', 'success');
    } catch (err) {
      console.error(err);
      showStatus(err.message || 'An error occurred while deleting.', 'error');
    }
  };

  const onPrimaryClick = () => {
    if (mode === 'edit') return onUpdateClick();
    if (mode === 'delete') return onDeleteClick();
    return onCreateClick();
  };

  // NEW: when child changes emails, sync local state & bubble up
  const handleEmailsChanged = (clientId, nextEmailList) => {
    if (!clientId) return;
    setSelectedGroupRow(prev => ({ ...(prev ?? {}), client: clientId, clientEmail: Array.isArray(nextEmailList) ? nextEmailList : [] }));
    onClientEmailsChanged?.(clientId, nextEmailList);
  };

  const renderButtonRow = () => (
    <CRow className="mt-3" style={{ maxWidth: 900, margin: '0 auto' }}>
      <CCol style={{ display: 'flex', justifyContent: 'flex-start' }}>
        <Button variant="outlined" size="small" onClick={() => setTabIndex((p) => Math.max(p - 1, 0))} disabled={tabIndex === 0}>
          Back
        </Button>
      </CCol>
      <CCol style={{ display: 'flex', justifyContent: 'flex-end', gap: '12px' }}>
        <Button variant="contained" size="small" color={mode === 'delete' ? 'error' : 'primary'} onClick={onPrimaryClick}>
          {mode === 'new' ? 'Create' : mode === 'edit' ? 'Update' : mode === 'delete' ? 'Delete' : 'Save'}
        </Button>
        <Button variant="outlined" size="small" onClick={() => setTabIndex((p) => Math.min(p + 1, 3))} disabled={tabIndex === 3}>
          Next
        </Button>
      </CCol>
    </CRow>
  );

  const borderPanelStyle = { border: '1px solid #ccc', borderRadius: '4px', padding: '8px', height: '440px', maxWidth: '900px', width: '100%', margin: '0 auto' };
  const noBorderPanelStyle = { border: '0px solid transparent', borderRadius: '4px', padding: '8px', height: '440px', maxWidth: '900px', width: '100%', margin: '0 auto' };

  return (
    <>
      <Box sx={{ display: 'flex', justifyContent: 'space-between', alignItems: 'center', backgroundColor: '#1976d2', color: 'white', height: 40, mx: -2, mt: -2, px: 0, py: 0, borderTopLeftRadius: 8, borderTopRightRadius: 8 }}>
        <Box sx={{ fontWeight: 600, fontSize: '0.95rem', lineHeight: '40px', pl: 2 }}>Client Information</Box>
        <IconButton onClick={onClose} size="small" sx={{ color: 'white', mr: 1 }}>
          <CloseIcon fontSize="small" />
        </IconButton>
      </Box>

      <Box sx={{ p: 2, height: 710, overflow: 'hidden', display: 'flex', flexDirection: 'column' }}>
        {/* Header inline inputs */}
        <Box sx={{ pb: 1, maxWidth: 900, mx: 'auto', width: '100%' }}>
          <CRow style={{ marginBottom: '12px', marginTop: 0 }}>
            <CCol xs="6">
              <Box sx={{ display: 'flex', alignItems: 'center', gap: 1 }}>
                <Box component="label" sx={{ fontSize: '0.78rem', minWidth: 72 }}>Client ID</Box>
                <TextField
                  label=""
                  value={viewRow.client ?? ''}
                  size="small"
                  disabled={!isEditable}
                  sx={{ ...sharedSx, minWidth: 160, flex: 1 }}
                  onChange={(e) =>
                    setSelectedGroupRow(prev => ({ ...(prev ?? makeEmptyClient()), client: e.target.value }))
                  }
                />
              </Box>
            </CCol>
            <CCol xs="6">
              <Box sx={{ display: 'flex', alignItems: 'center', gap: 1 }}>
                <Box component="label" sx={{ fontSize: '0.78rem', minWidth: 72 }}>Name</Box>
                <TextField
                  label=""
                  value={viewRow.name ?? ''}
                  size="small"
                  disabled={!isEditable}
                  sx={{ ...sharedSx, flex: 1 }}
                  onChange={(e) =>
                    setSelectedGroupRow(prev => ({ ...(prev ?? makeEmptyClient()), name: e.target.value }))
                  }
                />
              </Box>
            </CCol>
          </CRow>
        </Box>

        <Tabs value={tabIndex} onChange={(_e, v) => setTabIndex(v)} variant="fullWidth" sx={{ mt: -0.5, mb: 2 }} TabIndicatorProps={{ sx: { width: '30px', left: 'calc(50% - 15px)' } }}>
          <Tab label={<Box sx={{ display: 'flex', alignItems: 'center', gap: .5 }}><Box sx={{ width:18, height:18, borderRadius:'50%', backgroundColor:'#1976d2', color:'white', fontSize:'.7rem', display:'flex', alignItems:'center', justifyContent:'center' }}>1</Box>Client Information</Box>} sx={{ fontSize: '0.78rem', textTransform: 'none', minWidth: 205, maxWidth: 205, px: 1 }} />
          <Tab label={<Box sx={{ display: 'flex', alignItems: 'center', gap: .5 }}><Box sx={{ width:18, height:18, borderRadius:'50%', backgroundColor:'#1976d2', color:'white', fontSize:'.7rem', display:'flex', alignItems:'center', justifyContent:'center' }}>2</Box>Client Email Setup</Box>} sx={{ fontSize: '0.78rem', textTransform: 'none', minWidth: 160, maxWidth: 170, px: 1 }} />
          <Tab label={<Box sx={{ display: 'flex', alignItems: 'center', gap: .5 }}><Box sx={{ width:18, height:18, borderRadius:'50%', backgroundColor:'#1976d2', color:'white', fontSize:'.7rem', display:'flex', alignItems:'center', justifyContent:'center' }}>3</Box>Client Reports</Box>} sx={{ fontSize: '0.78rem', textTransform: 'none', minWidth: 135, maxWidth: 145, px: 1 }} />
          <Tab label={<Box sx={{ display: 'flex', alignItems: 'center', gap: .5 }}><Box sx={{ width:18, height:18, borderRadius:'50%', backgroundColor:'#1976d2', color:'white', fontSize:'.7rem', display:'flex', alignItems:'center', justifyContent:'center' }}>4</Box>Client ATM/Cash Prefixes</Box>} sx={{ fontSize: '0.78rem', textTransform: 'none', minWidth: 195, maxWidth: 205, px: 1 }} />
        </Tabs>

        <Box sx={{ flex: 1, overflow: 'auto' }}>
          {tabIndex === 0 && (
            <>
              <CRow className="mb-2" style={borderPanelStyle}>
                <CCol>
                  <div style={{ fontSize: '0.78rem', paddingTop: '12px', height: '100%' }}>
                    <EditClientInformation
                      selectedGroupRow={viewRow}
                      isEditable={isEditable}
                      setSelectedGroupRow={setSelectedGroupRow}
                      mode={mode}
                      onClientUpdated={onClientUpdated}
                    />
                  </div>
                </CCol>
              </CRow>
              {renderButtonRow()}
            </>
          )}

          {tabIndex === 1 && (
            <>
              <CRow className="mb-2" style={noBorderPanelStyle}>
                <CCol>
                  <div style={{ fontSize: '0.78rem', paddingTop: '12px', height: '100%' }}>
                    <EditClientEmailSetup
                      selectedGroupRow={viewRow}
                      isEditable={isEditable}
                      // NEW: pass handler down
                      onEmailsChanged={handleEmailsChanged}
                    />
                  </div>
                </CCol>
              </CRow>
              {renderButtonRow()}
            </>
          )}

          {tabIndex === 2 && (
            <>
              <CRow className="mb-2" style={noBorderPanelStyle}>
                <CCol>
                  <div style={{ fontSize: '0.78rem', paddingTop: '12px', height: '100%' }}>
                    <EditClientReport selectedGroupRow={viewRow} isEditable={isEditable} />
                  </div>
                </CCol>
              </CRow>
              {renderButtonRow()}
            </>
          )}

          {tabIndex === 3 && (
            <>
              <CRow className="mb-2" style={borderPanelStyle}>
                <CCol>
                  <div style={{ fontSize: '0.78rem', paddingTop: '12px', height: '80%' }}>
                    <EditAtmCashPrefix selectedGroupRow={viewRow} isEditable={isEditable} />
                  </div>
                </CCol>
              </CRow>
              {renderButtonRow()}
            </>
          )}
        </Box>
      </Box>

      <Snackbar open={status.open} autoHideDuration={3000} onClose={handleStatusClose} anchorOrigin={{ vertical: 'top', horizontal: 'center' }}>
        <Alert onClose={handleStatusClose} severity={status.severity} variant="filled" sx={{ width: '100%' }}>
          {status.message}
        </Alert>
      </Snackbar>
    </>
  );
};

export default ClientInformationWindow;



import React, { useEffect, useState } from 'react';
import {
  CCard, CCardBody, CRow, CCol, CFormSelect, CFormInput, CFormCheck,
} from '@coreui/react';
import { Button } from '@mui/material';

const EditClientEmailSetup = ({
  selectedGroupRow,
  isEditable,
  // NEW: callback to bubble changes up
  onEmailsChanged,
}) => {
  const [emailList, setEmailList] = useState([]);
  const [options, setOptions] = useState([]);
  const [selectedRecipients, setSelectedRecipients] = useState([]);

  const [name, setName] = useState('');
  const [emailAddress, setEmailAddress] = useState('');
  const [emailServer, setEmailServer] = useState('');
  const [reportId, setReportId] = useState('');

  const [isActive, setIsActive] = useState(false);
  const [isCC, setIsCC] = useState(false);
  const [statusMessage, setStatusMessage] = useState('');

  const emailServers = [
    'Omaha-SMTP Server (uschaappsmtp.1dc.com)',
    'Cha-SMTP Server (uschaappsmtp.1dc.com)',
  ];

  // Prefill from selectedGroupRow
  useEffect(() => {
    if (selectedGroupRow?.clientEmail?.length) {
      const list = selectedGroupRow.clientEmail;
      setEmailList(list);
      const formatted = list.map(
        (e) => `${e.emailNameTx} <${e.emailAddressTx}>${e.carbonCopyFlag ? ' (CC)' : ''}`
      );
      setOptions(formatted);
      setSelectedRecipients([formatted[0]]);
      updateFormFromEmail(list[0]);
    } else {
      resetForm();
    }
  }, [selectedGroupRow]);

  const updateFormFromEmail = (email) => {
    setName(email?.emailNameTx ?? '');
    setEmailAddress(email?.emailAddressTx ?? '');
    setEmailServer(emailServers[email?.mailServerId] ?? '');
    setReportId(email?.reportId ?? email?.id?.reportId ?? '');
    setIsActive(!!email?.activeFlag);
    setIsCC(!!email?.carbonCopyFlag);
  };

  const resetForm = () => {
    setName('');
    setEmailAddress('');
    setEmailServer('');
    setReportId('');
    setIsActive(false);
    setIsCC(false);
    setOptions([]);
    setSelectedRecipients([]);
    setEmailList([]);
  };

  const handleChange = (selectedOptions) => {
    const values = Array.from(selectedOptions).map((opt) => opt.value);
    setSelectedRecipients(values);
    if (values.length > 0) {
      const selected = values[0];
      const emailObj = emailList.find((email) =>
        selected.startsWith(`${email.emailNameTx} <${email.emailAddressTx}>`)
      );
      if (emailObj) updateFormFromEmail(emailObj);
    }
  };

  const handleUpdate = async () => {
    const clientId = selectedGroupRow?.client?.trim?.();
    if (!clientId) {
      setStatusMessage('Missing client ID. Please select a client before updating an email.');
      return;
    }
    if (!emailAddress?.trim()) {
      setStatusMessage('Email address is required.');
      return;
    }

    const mailServerId = emailServers.indexOf(emailServer);
    const payload = {
      clientId,
      reportId: reportId === '' ? 0 : Number(reportId),
      emailNameTx: name?.trim() || '',
      emailAddressTx: emailAddress.trim(),
      carbonCopyFlag: !!isCC,
      activeFlag: !!isActive,
      mailServerId: mailServerId === -1 ? 0 : mailServerId,
    };

    const selectedLabel = selectedRecipients[0] || '';
    const selectedEmailAddrMatch = selectedLabel.match(/<(.+?)>/);
    const previousEmailAddress = selectedEmailAddrMatch ? selectedEmailAddrMatch[1] : emailAddress.trim();

    try {
      const res = await fetch('http://localhost:8089/client-sysprin-writer/api/email/update', {
        method: 'PUT',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(payload),
      });
      if (!res.ok) throw new Error(`Failed to update email (${res.status})`);
      const updated = await res.json();

      // Build next list & update local state
      const nextList = (() => {
        const idx = emailList.findIndex(e => (e.emailAddressTx ?? e?.id?.emailAddressTx) === previousEmailAddress);
        if (idx === -1) return emailList;
        const copy = [...emailList];
        copy[idx] = updated;
        return copy;
      })();
      setEmailList(nextList);

      // Rebuild options and selection
      const respEmailAddr = updated.emailAddressTx ?? updated?.id?.emailAddressTx ?? emailAddress.trim();
      const newLabel = `${updated.emailNameTx ?? name} <${respEmailAddr}>${(updated.carbonCopyFlag ?? isCC) ? ' (CC)' : ''}`;
      const idxInOptions = emailList.findIndex(e => (e.emailAddressTx ?? e?.id?.emailAddressTx) === previousEmailAddress);
      const newOptions = [...options];
      if (idxInOptions !== -1) newOptions[idxInOptions] = newLabel;
      setOptions(newOptions);
      setSelectedRecipients([newLabel]);

      // NEW: bubble up
      onEmailsChanged?.(clientId, nextList);

      setStatusMessage('Email updated successfully');
    } catch (err) {
      console.error('Error updating email:', err);
      setStatusMessage('Error updating email');
    }
  };

  const handleRemove = async () => {
    if (!selectedRecipients.length) return;

    const clientId = selectedGroupRow?.client?.trim?.();
    if (!clientId) {
      setStatusMessage('Missing client ID.');
      return;
    }

    const selectedLabel = selectedRecipients[0];
    const emailObj = emailList.find(
      (email) => selectedLabel.startsWith(`${email.emailNameTx} <${email.emailAddressTx}>`)
    );
    if (!emailObj) return;

    try {
      const res = await fetch(
        `http://localhost:8089/client-sysprin-writer/api/email/delete?clientId=${clientId}&emailAddress=${encodeURIComponent(emailObj.emailAddressTx)}`,
        { method: 'DELETE' }
      );
      if (!res.ok) throw new Error('Failed to delete email');

      const updatedList = emailList.filter((e) => e.emailAddressTx !== emailObj.emailAddressTx);
      setEmailList(updatedList);

      const updatedOptions = updatedList.map(
        (email) => `${email.emailNameTx} <${email.emailAddressTx}>${email.carbonCopyFlag ? ' (CC)' : ''}`
      );
      setOptions(updatedOptions);

      if (updatedList.length > 0) {
        const nextEmail = updatedList[0];
        const nextLabel = `${nextEmail.emailNameTx} <${nextEmail.emailAddressTx}>${nextEmail.carbonCopyFlag ? ' (CC)' : ''}`;
        setSelectedRecipients([nextLabel]);
        updateFormFromEmail(nextEmail);
      } else {
        resetForm();
      }

      // NEW: bubble up
      onEmailsChanged?.(clientId, updatedList);

      setStatusMessage('Email removed successfully');
    } catch (err) {
      console.error('Error removing email:', err);
      setStatusMessage('Error removing email');
    }
  };

  const handleAdd = async () => {
    const clientId = selectedGroupRow?.client?.trim?.();
    if (!clientId) {
      setStatusMessage('Missing client ID. Please select a client before adding an email.');
      return;
    }
    if (!emailAddress?.trim()) {
      setStatusMessage('Email address is required.');
      return;
    }

    const mailServerId = emailServers.indexOf(emailServer);
    const payload = {
      clientId,
      reportId: reportId === '' ? 0 : Number(reportId),
      emailNameTx: name?.trim() || '',
      emailAddressTx: emailAddress.trim(),
      carbonCopyFlag: !!isCC,
      activeFlag: !!isActive,
      mailServerId: mailServerId === -1 ? 0 : mailServerId,
    };

    try {
      const res = await fetch(`http://localhost:8089/client-sysprin-writer/api/email/add`, {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(payload),
      });
      if (!res.ok) throw new Error(`Failed to add email (${res.status})`);

      const newEmail = await res.json();

      const respEmailAddr = newEmail.emailAddressTx ?? newEmail?.id?.emailAddressTx ?? emailAddress.trim();
      const formatted = `${newEmail.emailNameTx ?? name} <${respEmailAddr}>${(newEmail.carbonCopyFlag ?? isCC) ? ' (CC)' : ''}`;

      const nextList = [...emailList, newEmail];
      setEmailList(nextList);
      setOptions((prev) => [...prev, formatted]);
      setSelectedRecipients([formatted]);

      // NEW: bubble up
      onEmailsChanged?.(clientId, nextList);

      setStatusMessage('Email added successfully');
    } catch (err) {
      console.error('Error adding email:', err);
      setStatusMessage('Error adding email');
    }
  };

  return (
    <CRow style={{ fontSize: '0.78rem', maxHeight: 420, overflowY: 'auto', marginBottom: 0 }}>
      <CCol xs={12}>
        <CCard>
          <CCardBody style={{ padding: 6 }}>
            <div className="mb-3" style={{ fontSize: '0.78rem', display: 'grid', gridTemplateColumns: 'auto auto', columnGap: '16px', alignItems: 'start' }}>
              <div style={{ gridRow: '1 / span 5' }}>
                <div style={{ marginLeft: 8 }}>
                  <div style={{ marginBottom: 12 }}>Email Recipients:</div>
                  <CFormSelect
                    multiple
                    value={selectedRecipients}
                    onChange={(e) => handleChange(e.target.selectedOptions)}
                    disabled={!isEditable}
                    style={{ fontSize: '0.78rem', height: 220, maxWidth: 400, width: 400, marginLeft: 0 }}
                  >
                    {options.map((email, idx) => (
                      <option key={idx} value={email} style={{ fontSize: '0.78rem' }}>
                        {email}
                      </option>
                    ))}
                  </CFormSelect>
                </div>
              </div>

              <div style={{ minHeight: 64 }}>
                <div style={{ marginBottom: 6 }}>Email Server:</div>
                <CFormSelect
                  value={emailServer}
                  onChange={(e) => setEmailServer(e.target.value)}
                  disabled={!isEditable}
                  style={{ fontSize: '0.78rem', width: 260 }}
                >
                  <option value="">Select Email Server</option>
                  {emailServers.map((srv, idx) => (
                    <option key={idx} value={srv}>{srv}</option>
                  ))}
                </CFormSelect>
              </div>

              <div style={{ minHeight: 64, display: 'flex', alignItems: 'center', gap: '24px' }}>
                <CFormCheck label="Recipient Active" checked={isActive} onChange={(e) => setIsActive(e.target.checked)} disabled={!isEditable} style={{ fontSize: '0.78rem' }} />
                <CFormCheck label="CC" checked={isCC} onChange={(e) => setIsCC(e.target.checked)} disabled={!isEditable} style={{ fontSize: '0.78rem' }} />
              </div>

              <div style={{ minHeight: 64 }}>
                <div style={{ marginBottom: 6 }}>Name:</div>
                <CFormInput value={name} onChange={(e) => setName(e.target.value)} disabled={!isEditable} placeholder="Enter name" style={{ fontSize: '0.78rem', width: 260, marginLeft: 0 }} />
              </div>

              <div style={{ minHeight: 64 }}>
                <div style={{ marginBottom: 6 }}>Report ID:</div>
                <CFormInput value={reportId} onChange={(e) => setReportId(e.target.value)} placeholder="Enter report id" disabled={!isEditable} style={{ fontSize: '0.78rem', width: 140 }} type="number" inputMode="numeric" />
              </div>

              <div style={{ minHeight: 64 }}>
                <div style={{ marginBottom: 6 }}>Email Address:</div>
                <CFormInput value={emailAddress} onChange={(e) => setEmailAddress(e.target.value)} placeholder="Enter email address" disabled={!isEditable} style={{ fontSize: '0.78rem', width: 260 }} type="email" />
              </div>
            </div>

            <CRow style={{ fontSize: '0.78rem', marginBottom: 8 }}>
              <CCol>
                <div
                  style={{
                    height: 22, lineHeight: '22px', overflow: 'hidden', whiteSpace: 'nowrap', textOverflow: 'ellipsis',
                    fontWeight: 'bold', visibility: statusMessage ? 'visible' : 'hidden',
                    color: statusMessage?.toLowerCase().includes('error') ? '#d32f2f' : '#2e7d32',
                  }}
                  aria-live="polite"
                >
                  {statusMessage || ''}
                </div>
              </CCol>
            </CRow>

            <CRow className="mt-2" style={{ marginTop: 8 }}>
              <CCol className="d-flex justify-content-center" style={{ gap: 8, paddingTop: 2, paddingBottom: 2 }} >
                <Button variant="outlined" size="small" onClick={handleUpdate} sx={{ fontSize: '0.78rem', textTransform: 'none' }}>
                  Update
                </Button>
                <Button variant="outlined" size="small" onClick={handleAdd} sx={{ fontSize: '0.78rem', textTransform: 'none' }} color="primary">
                  Add
                </Button>
                <Button variant="outlined" size="small" onClick={handleRemove} sx={{ fontSize: '0.78rem', textTransform: 'none' }} color="error">
                  Remove
                </Button>
              </CCol>
            </CRow>
          </CCardBody>
        </CCard>
      </CCol>
    </CRow>
  );
};

export default EditClientEmailSetup;




// EditClientReport.jsx
import React, { useEffect, useMemo, useState } from 'react';
import { Typography, Button, IconButton, Tooltip } from '@mui/material';
import InfoOutlinedIcon from '@mui/icons-material/InfoOutlined';
import EditOutlinedIcon from '@mui/icons-material/EditOutlined';
import DeleteOutlineIcon from '@mui/icons-material/DeleteOutline';
import { CCard, CCardBody } from '@coreui/react';

import ClientReportWindow from './utils/ClientReportWindow';

const PAGE_SIZE = 10;

const EditClientReport = ({ selectedGroupRow, isEditable }) => {
  const [tableData, setTableData] = useState([]);
  const [page, setPage] = useState(0);

  // modal for detail/edit/new/delete
  const [modalOpen, setModalOpen] = useState(false);
  const [modalMode, setModalMode] = useState('detail'); // 'detail' | 'edit' | 'new' | 'delete'
  const [modalRowIdx, setModalRowIdx] = useState(null); // null => creating new
  const [draftRow, setDraftRow] = useState(null);       // holds the "New" row or snapshot after delete

  // pagination
  const pageCount = useMemo(() => Math.ceil((tableData?.length || 0) / PAGE_SIZE), [tableData]);
  const pageData = useMemo(
    () => tableData.slice(page * PAGE_SIZE, (page + 1) * PAGE_SIZE),
    [tableData, page]
  );

  // load/normalize incoming data
  useEffect(() => {
    const options = Array.isArray(selectedGroupRow?.reportOptions)
      ? selectedGroupRow.reportOptions
      : [];

    const clientData = options.map((option) => ({
      reportName: option?.reportDetails?.queryName || '',
      reportId: option?.reportDetails?.reportId ?? option?.reportId ?? null,
      receive: option?.receiveFlag ? '1' : '2', // 1=Yes, 2=No
      destination: option?.outputTypeCd !== undefined ? String(option.outputTypeCd) : '0', // 0=None,1=File,2=Print
      fileText: option?.fileTypeCd !== undefined ? String(option.fileTypeCd) : '0',       // 0=None,1=Text,2=Excel
      email: option?.emailFlag === 1 ? '1' : option?.emailFlag === 2 ? '2' : '0',        // 0=None,1=Email,2=Web
      password: option?.reportPasswordTx || '',
      emailBodyTx: option?.emailBodyTx || '',
    }));

    setTableData(clientData);
    const lastPage = Math.max(Math.ceil(clientData.length / PAGE_SIZE) - 1, 0);
    setPage((p) => Math.min(p, lastPage));
  }, [selectedGroupRow]);

  // styles
  const rowCellStyle = {
    backgroundColor: 'white',
    fontSize: '0.72rem',
    lineHeight: 1.1,
    padding: '2px 6px',
    borderBottom: '1px dotted #ddd',
    display: 'flex',
    alignItems: 'center',
    whiteSpace: 'nowrap',
    overflow: 'hidden',
    textOverflow: 'ellipsis',
  };
  const headerCellStyle = {
    backgroundColor: '#f0f0f0',
    fontWeight: 'bold',
    fontSize: '0.75rem',
    padding: '2px 6px',
    borderBottom: '1px dotted #ccc',
  };
  const labelCell = (text, align = 'center') => (
    <div style={{ ...rowCellStyle, justifyContent: align }}>
      <Typography noWrap sx={{ fontSize: '0.72rem', lineHeight: 1.1 }}>
        {text}
      </Typography>
    </div>
  );

  // label mappers
  const mapReceive = (v) => (v === '1' ? 'Yes' : v === '2' ? 'No' : 'None');
  const mapDestination = (v) => (v === '1' ? 'File' : v === '2' ? 'Print' : 'None');

  // actions -> detail/edit/delete
  const openDetail = (rowIdx) => {
    setModalMode('detail');
    setModalRowIdx(rowIdx);
    setDraftRow(null);
    setModalOpen(true);
  };
  const openEdit = (rowIdx) => {
    setModalMode('edit');
    setModalRowIdx(rowIdx);
    setDraftRow(null);
    setModalOpen(true);
  };
  const openDelete = (rowIdx) => {
    setModalMode('delete');
    setModalRowIdx(rowIdx);
    setDraftRow(null);
    setModalOpen(true);
  };

  // actual deletion logic (parent table update) — KEEP DIALOG OPEN
  const handleRemove = (rowIdx) => {
    setTableData((prev) => {
      const next = prev.filter((_, i) => i !== rowIdx);
      const lastPage = Math.max(Math.ceil(next.length / PAGE_SIZE) - 1, 0);
      setPage((p) => Math.min(p, lastPage));
      return next;
    });
  };

  // "New" button -> open modal in new mode with blank row
  const handleNewClick = () => {
    if (!isEditable) return;
    setModalMode('new');
    setModalRowIdx(null); // null signals "create"
    setDraftRow({
      reportName: '',
      reportId: null,
      receive: '0',
      destination: '0',
      fileText: '0',
      email: '0',
      password: '',
      emailBodyTx: ''
    });
    setModalOpen(true);
  };

  // save/create from modal
  const handleSaveFromModal = (updatedRow) => {
    if (modalRowIdx == null) {
      // creating a new row
      const normalized = {
        reportName: updatedRow?.reportName ?? '',
        reportId: updatedRow?.reportId ?? null,
        receive: updatedRow?.receive != null ? String(updatedRow.receive) : '0',
        destination: updatedRow?.destination != null ? String(updatedRow.destination) : '0',
        fileText: updatedRow?.fileText != null ? String(updatedRow.fileText) : '0',
        email: updatedRow?.email != null ? String(updatedRow.email) : '0',
        password: updatedRow?.password ?? '',
        emailBodyTx: updatedRow?.emailBodyTx ?? '',
      };
      setTableData((prev) => {
        const next = [...prev, normalized];
        const lastPage = Math.max(Math.ceil(next.length / PAGE_SIZE) - 1, 0);
        setPage(lastPage);
        return next;
      });
      // keep dialog open; no changes to modal state
    } else {
      // updating existing row
      setTableData((prev) => {
        const next = [...prev];
        next[modalRowIdx] = {
          ...next[modalRowIdx],
          ...updatedRow,
          reportId: updatedRow?.reportId ?? next[modalRowIdx].reportId ?? null,
        };
        return next;
      });
      // keep dialog open
    }
  };

  // delete confirm from modal (called after successful DELETE)
  const handleDeleteFromModal = () => {
    if (modalRowIdx == null) return;

    // snapshot the row being shown so dialog content stays visible after removal
    const snapshot = tableData[modalRowIdx] || null;
    setDraftRow(snapshot);
    setModalRowIdx(null); // so currentRow resolves to draftRow after removal

    // update table (do NOT close dialog)
    handleRemove(modalRowIdx);
  };

  const currentRow = modalRowIdx == null ? draftRow : tableData[modalRowIdx] || null;

  // Try to derive a clientId to pass down; adjust keys to your actual data shape
  const clientId =
    selectedGroupRow?.client ||
    selectedGroupRow?.clientId ||
    selectedGroupRow?.billingSp ||
    '';

  return (
    <div style={{ padding: '10px' }}>
      <CCard>
        <CCardBody>
          {/* pagination (top) */}
          <div
            style={{
              marginBottom: '6px',
              display: 'flex',
              justifyContent: 'space-between',
              alignItems: 'center',
            }}
          >
            <Button
              variant="text"
              size="small"
              sx={{ fontSize: '0.68rem', padding: '2px 6px', minWidth: 'unset', textTransform: 'none' }}
              onClick={() => setPage((p) => Math.max(p - 1, 0))}
              disabled={page === 0 || pageCount === 0}
            >
              ◀ Previous
            </Button>
            <Typography fontSize="0.72rem">
              Page {tableData.length > 0 ? page + 1 : 0} of {tableData.length > 0 ? pageCount : 0}
            </Typography>
            <Button
              variant="text"
              size="small"
              sx={{ fontSize: '0.68rem', padding: '2px 6px', minWidth: 'unset', textTransform: 'none' }}
              onClick={() => setPage((p) => Math.min(p + 1, pageCount - 1))}
              disabled={page >= pageCount - 1 || pageCount === 0}
            >
              Next ▶
            </Button>
          </div>

          {/* table */}
          <div style={{ height: '320px', marginBottom: '4px', marginTop: '2px' }}>
            <div
              style={{
                display: 'grid',
                gridTemplateColumns: '4fr 1fr 1fr 3fr',
                columnGap: '4px',
              }}
            >
              {/* headers */}
              {['Report Name', 'Receive', 'Destination', 'Action'].map((header) => (
                <div
                  key={header}
                  style={{
                    ...headerCellStyle,
                    textAlign: header === 'Action' ? 'center' : 'left',
                  }}
                >
                  {header}
                </div>
              ))}

              {/* rows */}
              {pageData.map((item, index) => {
                const rowIdx = page * PAGE_SIZE + index;
                return (
                  <React.Fragment key={`${item.reportName}-${rowIdx}`}>
                    <div style={rowCellStyle}>{item.reportName}</div>
                    {labelCell(mapReceive(item.receive))}
                    {labelCell(mapDestination(item.destination))}
                    <div style={{ ...rowCellStyle, gap: 4, justifyContent: 'center' }}>
                      <Tooltip title="Detail" arrow>
                        <IconButton
                          aria-label="detail"
                          size="small"
                          sx={{ p: 0.25, fontSize: '0.95rem' }}
                          onClick={() => openDetail(rowIdx)}
                        >
                          <InfoOutlinedIcon fontSize="inherit" />
                        </IconButton>
                      </Tooltip>
                      <Tooltip title="Edit" arrow>
                        <span>
                          <IconButton
                            aria-label="edit"
                            size="small"
                            sx={{ p: 0.25, fontSize: '0.95rem' }}
                            onClick={() => openEdit(rowIdx)}
                            disabled={!isEditable}
                          >
                            <EditOutlinedIcon fontSize="inherit" />
                          </IconButton>
                        </span>
                      </Tooltip>
                      <Tooltip title="Remove" arrow>
                        <span>
                          <IconButton
                            aria-label="remove"
                            size="small"
                            sx={{ p: 0.25, fontSize: '0.95rem' }}
                            onClick={() => openDelete(rowIdx)}
                            disabled={!isEditable}
                          >
                            <DeleteOutlineIcon fontSize="inherit" />
                          </IconButton>
                        </span>
                      </Tooltip>
                    </div>
                  </React.Fragment>
                );
              })}
            </div>
          </div>

          {/* "New" button centered horizontally */}
          <div style={{ marginTop: '8px', display: 'flex', justifyContent: 'center' }}>
            <Button
              variant="contained"
              size="small"
              onClick={handleNewClick}
              disabled={!isEditable}
            >
              New
            </Button>
          </div>
        </CCardBody>
      </CCard>

      {/* detail/edit/new/delete window */}
      <ClientReportWindow
        open={modalOpen}
        mode={modalMode}
        row={currentRow}
        clientId={clientId}
        onClose={() => {
          setModalOpen(false);
          setDraftRow(null);
        }}
        onSave={handleSaveFromModal}          // used for 'edit' and 'new'
        onDelete={handleDeleteFromModal}      // used after successful DELETE (keeps dialog open)
      />
    </div>
  );
};

export default EditClientReport;




