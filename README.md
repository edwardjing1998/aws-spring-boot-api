// ClientInformationWindow.jsx
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
}) => {
  const [tabIndex, setTabIndex] = useState(0);
  const [isEditable, setIsEditable] = useState(true);

  // ---- helpers -------------------------------------------------------------
  const makeEmptyClient = () => ({
    client: '',
    name: '',
    addr: '',
    city: '',
    state: '',
    zip: '',
    contact: '',
    phone: '',
    active: true,
    faxNumber: '',
    billingSp: '',
    reportBreakFlag: 0,
    chLookUpType: 0,
    excludeFromReport: false,
    positiveReports: false,
    subClientInd: false,
    subClientXref: '',
    amexIssued: false,
  });

  // normalize payload for create/update
  const buildPayload = (row) => {
    const {
      client,
      name,
      addr,
      city,
      state,
      zip,
      contact,
      phone,
      active,
      faxNumber,
      billingSp,
      reportBreakFlag,
      chLookUpType,
      excludeFromReport,
      positiveReports,
      subClientInd,
      subClientXref,
      amexIssued,
    } = row || {};

    return {
      client,
      name,
      addr,
      city,
      state,
      zip,
      contact,
      phone,
      active: !!active,
      faxNumber,
      billingSp,
      reportBreakFlag:
        typeof reportBreakFlag === 'string' ? Number(reportBreakFlag) : (reportBreakFlag ?? 0),
      chLookUpType:
        typeof chLookUpType === 'string' ? Number(chLookUpType) : (chLookUpType ?? 0),
      excludeFromReport: !!excludeFromReport,
      positiveReports: !!positiveReports,
      subClientInd: !!subClientInd,
      subClientXref,
      amexIssued: !!amexIssued,
    };
  };

  // ---- seed state by mode --------------------------------------------------
  useEffect(() => {
    if (mode === 'new') {
      setIsEditable(true);
      setSelectedGroupRow(prev =>
        prev && Object.keys(prev).length ? prev : makeEmptyClient()
      );
    } else if (mode === 'edit') {
      setIsEditable(true);
    } else {
      // view/delete
      setIsEditable(false);
    }
  }, [mode, setSelectedGroupRow]);

  const viewRow = mode === 'new'
    ? (selectedGroupRow ?? makeEmptyClient())
    : (selectedGroupRow ?? {});

  // ---- shared MUI sx -------------------------------------------------------
  const sharedSx = {
    '& .MuiInputBase-root': { height: '30px', fontSize: '0.78rem' },
    '& .MuiInputBase-input': { padding: '4px 4px', height: '30px', fontSize: '0.78rem', lineHeight: '1rem' },
    '& .MuiInputLabel-root': { fontSize: '0.78rem', lineHeight: '1rem' },
    '& .MuiInputBase-input.Mui-disabled': { color: 'black', WebkitTextFillColor: 'black' },
    '& .MuiInputLabel-root.Mui-disabled': { color: 'black' },
  };

  // ---- tab handlers --------------------------------------------------------
  const handleTabChange = (_e, newValue) => setTabIndex(newValue);
  const handleNext = () => setTabIndex(prev => Math.min(prev + 1, 3));
  const handleBack = () => setTabIndex(prev => Math.max(prev - 1, 0));

  // ---- API handlers (create/update/delete) ---------------------------------
  // CREATE (for mode === 'new')

  const [status, setStatus] = useState({ open: false, severity: 'info', message: '' });

  const showStatus = (message, severity = 'success') =>
    setStatus({ open: true, severity, message });

  const handleStatusClose = (_e, reason) => {
    if (reason === 'clickaway') return;
    setStatus(prev => ({ ...prev, open: false }));
  };
  

   // ---- API handlers (create/update/delete) ---------------------------------
  // CREATE (for mode === 'new')
   const onCreateClick = async () => {
       try {
         const saved = await handleCreate(viewRow);
         console.log('Client created successfully:', saved);
         showStatus('Client created successfully', 'success');
       } catch (err) {
         console.error(err);
         showStatus(err.message || 'An error occurred while creating.', 'error');
      }
   };

  // UPDATE (for mode === 'edit')
  // Uses the endpoint you showed earlier in EditClientInformation
   const onUpdateClick = async () => {
       try {
         const saved = await handleUpdate(viewRow);
         console.log('Client updated successfully:', saved);
         showStatus('Client updated successfully', 'success'); // ✅ fix message
       } catch (err) {
         console.error(err);
         showStatus(err.message || 'An error occurred while updating.', 'error');
       }
     };
    

  // DELETE (for mode === 'delete')
  // Adjust the endpoint to match your backend; this is a common pattern.
   const onDeleteClick = async () => {
       if (!viewRow?.client) {
        showStatus('Client ID is required to delete.', 'warning');
        return;
      }
      if (!window.confirm(`Delete client "${viewRow.client}"? This cannot be undone.`)) return;
      try {
       await handleDelete(viewRow.client);
       console.log('Client deleted successfully.');
       showStatus('Client deleted successfully', 'success');
 //      onClose?.();
       } catch (err) {
         console.error(err);
        showStatus(err.message || 'An error occurred while deleting.', 'error');
      }
   };

  // Primary button click routes to the right handler by mode
   const onPrimaryClick = () => {
       if (mode === 'edit') return onUpdateClick();
       if (mode === 'delete') return onDeleteClick();
       return onCreateClick();
     };

  // ---- button label & color ------------------------------------------------
  const actionLabel =
    mode === 'new' ? 'Create' :
    mode === 'edit' ? 'Update' :
    mode === 'delete' ? 'Delete' :
    'Save';

  const actionColor = mode === 'delete' ? 'error' : 'primary';

  // ---- shared panel styles -------------------------------------------------
  const renderButtonRow = () => (
    <CRow className="mt-3" style={{ maxWidth: 900, margin: '0 auto' }}>
      <CCol style={{ display: 'flex', justifyContent: 'flex-start' }}>
        <Button variant="outlined" size="small" onClick={handleBack} disabled={tabIndex === 0}>
          Back
        </Button>
      </CCol>
      <CCol style={{ display: 'flex', justifyContent: 'flex-end', gap: '12px' }}>
        <Button
          variant="contained"
          size="small"
          color={actionColor}
          onClick={onPrimaryClick}
        >
          {actionLabel}
        </Button>
        <Button variant="outlined" size="small" onClick={handleNext} disabled={tabIndex === 3}>
          Next
        </Button>
      </CCol>
    </CRow>
  );

  const borderPanelStyle = {
    border: '1px solid #ccc',
    borderRadius: '4px',
    padding: '8px',
    height: '440px',
    maxWidth: '900px',
    width: '100%',
    margin: '0 auto',
  };

  const noBorderPanelStyle = {
    border: '0px solid transparent',
    borderRadius: '4px',
    padding: '8px',
    height: '440px',
    maxWidth: '900px',
    width: '100%',
    margin: '0 auto',
  };

  // ---- render --------------------------------------------------------------
  return (
    <>
      {/* Full-width header OUTSIDE the container */}
      <Box
        sx={{
          display: 'flex',
          justifyContent: 'space-between',
          alignItems: 'center',
          backgroundColor: '#1976d2',
          color: 'white',
          height: 40,
          mx: -2,
          mt: -2,
          px: 0,
          py: 0,
          borderTopLeftRadius: 8,
          borderTopRightRadius: 8,
        }}
      >
        <Box sx={{ fontWeight: 600, fontSize: '0.95rem', lineHeight: '40px', pl: 2 }}>
          Client Information
        </Box>
        <IconButton onClick={onClose} size="small" sx={{ color: 'white', mr: 1 }}>
          <CloseIcon fontSize="small" />
        </IconButton>
      </Box>

      {/* Main container (body) */}
      <Box sx={{ p: 2, height: 710, overflow: 'hidden', display: 'flex', flexDirection: 'column' }}>
        {/* Inputs under header (inline labels + fields on the same row) */}
        <Box sx={{ pb: 1, maxWidth: 900, mx: 'auto', width: '100%' }}>
          <CRow style={{ marginBottom: '12px', marginTop: 0 }}>
            {/* Client ID */}
            <CCol xs="6">
              <Box sx={{ display: 'flex', alignItems: 'center', gap: 1 }}>
                <Box component="label" sx={{ fontSize: '0.78rem', minWidth: 72 }}>
                  Client ID
                </Box>
                <TextField
                  label=""
                  value={viewRow.client ?? ''}
                  size="small"
                  disabled={!isEditable}
                  sx={{ ...sharedSx, minWidth: 160, flex: 1 }}
                  onChange={(e) =>
                    setSelectedGroupRow(prev => ({
                      ...(prev ?? makeEmptyClient()),
                      client: e.target.value,
                    }))
                  }
                />
              </Box>
            </CCol>

            {/* Name */}
            <CCol xs="6">
              <Box sx={{ display: 'flex', alignItems: 'center', gap: 1 }}>
                <Box component="label" sx={{ fontSize: '0.78rem', minWidth: 72 }}>
                  Name
                </Box>
                <TextField
                  label=""
                  value={viewRow.name ?? ''}
                  size="small"
                  disabled={!isEditable}
                  sx={{ ...sharedSx, flex: 1 }}
                  onChange={(e) =>
                    setSelectedGroupRow(prev => ({
                      ...(prev ?? makeEmptyClient()),
                      name: e.target.value,
                    }))
                  }
                />
              </Box>
            </CCol>
          </CRow>
        </Box>

        {/* Tabs */}
        <Tabs
          value={tabIndex}
          onChange={handleTabChange}
          variant="fullWidth"
          sx={{ mt: -0.5, mb: 2 }}
          TabIndicatorProps={{ sx: { width: '30px', left: 'calc(50% - 15px)' } }}
        >
          <Tab
            label={
              <Box sx={{ display: 'flex', alignItems: 'center', gap: 0.5 }}>
                <Box sx={{ width: 18, height: 18, borderRadius: '50%', backgroundColor: '#1976d2', color: 'white', fontSize: '0.7rem', display: 'flex', alignItems: 'center', justifyContent: 'center' }}>
                  1
                </Box>
                Client Information
              </Box>
            }
            sx={{ fontSize: '0.78rem', textTransform: 'none', minWidth: 205, maxWidth: 205, px: 1 }}
          />
          <Tab
            label={
              <Box sx={{ display: 'flex', alignItems: 'center', gap: 0.5 }}>
                <Box sx={{ width: 18, height: 18, borderRadius: '50%', backgroundColor: '#1976d2', color: 'white', fontSize: '0.7rem', display: 'flex', alignItems: 'center', justifyContent: 'center' }}>
                  2
                </Box>
                Client Email Setup
              </Box>
            }
            sx={{ fontSize: '0.78rem', textTransform: 'none', minWidth: 160, maxWidth: 170, px: 1 }}
          />
          <Tab
            label={
              <Box sx={{ display: 'flex', alignItems: 'center', gap: 0.5 }}>
                <Box sx={{ width: 18, height: 18, borderRadius: '50%', backgroundColor: '#1976d2', color: 'white', fontSize: '0.7rem', display: 'flex', alignItems: 'center', justifyContent: 'center' }}>
                  3
                </Box>
                Client Reports
              </Box>
            }
            sx={{ fontSize: '0.78rem', textTransform: 'none', minWidth: 135, maxWidth: 145, px: 1 }}
          />
          <Tab
            label={
              <Box sx={{ display: 'flex', alignItems: 'center', gap: 0.5 }}>
                <Box sx={{ width: 18, height: 18, borderRadius: '50%', backgroundColor: '#1976d2', color: 'white', fontSize: '0.7rem', display: 'flex', alignItems: 'center', justifyContent: 'center' }}>
                  4
                </Box>
                Client ATM/Cash Prefixes
              </Box>
            }
            sx={{ fontSize: '0.78rem', textTransform: 'none', minWidth: 195, maxWidth: 205, px: 1 }}
          />
        </Tabs>

        {/* Tab content */}
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
                    <EditClientEmailSetup selectedGroupRow={viewRow} isEditable={isEditable} />
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

      <Snackbar
          open={status.open}
          autoHideDuration={3000}
          onClose={handleStatusClose}
          anchorOrigin={{ vertical: 'top', horizontal: 'center' }}
        >
          <Alert
            onClose={handleStatusClose}
            severity={status.severity}
            variant="filled"
            sx={{ width: '100%' }}
          >
            {status.message}
          </Alert>
      </Snackbar>


    </>
  );
};

export default ClientInformationWindow;





import React, { useState, useEffect, useMemo, useCallback } from 'react';
import { CRow, CCol, CCard, CCardBody } from '@coreui/react';
import { Button, Modal, Box } from '@mui/material';

import AutoCompleteInputBox from '../../../components/ClientAutoCompleteInputBox';
import PreviewSysPrinInformation from './sys-prin-config/PreviewSysPrinInformation';
import PreviewClientInformation from './PreviewClientInformation';
import { defaultSelectedData, mapRowDataToSelectedData } from './utils/SelectedData';
import NavigationPanel from './NavigationPanel';
import { fetchClientsPaging, fetchWildcardPage } from './utils/ClientIntegrationService';

import ClientInformationWindow from './utils/ClientInformationWindow';
import SysPrinInformationWindow from './utils/SysPrinInformationWindow';

const ClientInformationPage = () => {
  const [clientList, setClientList] = useState([]);
  const [currentPage, setCurrentPage] = useState(0);
  const [inputValue, setInputValue] = useState('');
  const [isWildcardMode, setIsWildcardMode] = useState(false);
  const [selectedGroupRow, setSelectedGroupRow] = useState(null);
  const [selectedData, setSelectedData] = useState(defaultSelectedData);

  const [clientInformationWindow, setClientInformationWindow] = useState({ open: false, mode: 'edit' });
  const [sysPrinInformationWindow, setSysPrinInformationWindow] = useState({ open: false, mode: 'edit' });
  const [clientEditActionsDisabled, setClientEditActionsDisabled] = useState(true);

  // ---- fetch initial clients (paged) ----
  useEffect(() => {
    fetchClientsPaging(currentPage, 25)
      .then((data) => setClientList(Array.isArray(data) ? data : []))
      .catch((error) => {
        console.error('Error fetching clients:', error);
        alert(`Error fetching client details: ${error.message}`);
      });
  }, [currentPage]);

  // ---- map: billingSp -> client record ----
  const clientMap = useMemo(() => {
    const map = new Map();
    clientList.forEach((client) => {
      map.set(client.billingSp, client);
    });
    return map;
  }, [clientList]);

  // ---- autocomplete callback replaces client list ----
  const handleClientsFetched = (fetchedClients) => {
    const list = Array.isArray(fetchedClients) ? fetchedClients : [];
    setCurrentPage(0);
    setClientList((prev) => {
      const prevIds = prev.map((c) => c.client).join(',');
      const newIds = list.map((c) => c.client).join(',');
      return prevIds === newIds ? prev : list;
    });
  };

  // ---- when user clicks rows in the nav grid ----
  const handleRowClick = (rowData) => {
    if (rowData.isGroup) {
      setClientEditActionsDisabled(false);
      setSelectedGroupRow(rowData);
      return;
    }

    const billingSp = rowData.billingSp || '';
    const matchedClient = clientMap.get(billingSp);
    const atmCashPrefixes = matchedClient?.sysPrinsPrefixes || [];
    const clientEmails = matchedClient?.clientEmail || [];
    const reportOptions = matchedClient?.reportOptions || [];
    const sysPrinsList = matchedClient?.sysPrins || [];

    const mappedData = mapRowDataToSelectedData(
      selectedData,
      rowData,
      atmCashPrefixes,
      clientEmails,
      reportOptions,
      sysPrinsList
    );
    setSelectedData(mappedData);
  };

  // =========================
  // Helpers for syncing edits
  // =========================

  // Normalize a vendor record into the parent "canonical" shape
  const normalizeVendorSliceItem = useCallback(
    (v) => {
      const id = String(v?.vendorId ?? v?.vendId ?? v?.vendor?.vendId ?? v?.vendor?.id ?? '');
      const name =
        v?.vendName ??
        v?.vendorName ??
        v?.vendor?.vendNm ??
        v?.vendor?.name ??
        String(id);
      const q =
        typeof (v?.queueForMail ?? v?.queForMail ?? v?.queForMailCd) === 'string'
          ? ['1', 'Y', 'TRUE'].includes(String(v?.queueForMail ?? v?.queForMail ?? v?.queForMailCd).toUpperCase())
          : !!(v?.queueForMail ?? v?.queForMail);

      const sysPrin = String(selectedData?.sysPrin ?? '');

      return {
        vendorId: id,
        vendId: id,
        vendName: name,
        queueForMail: q,
        queForMail: q,
        queForMailCd: q ? 'Y' : 'N',
        vendor: { vendId: id, vendNm: name },
        ...(sysPrin ? { id: { sysPrin, vendorId: id } } : {}),
      };
    },
    [selectedData?.sysPrin]
  );

  // Patch the matching sysPrin object inside clientList (source-of-truth used by handleRowClick)
  const patchSysPrinSlice = useCallback(
    (sysPrin, sliceName, nextArrayRaw) => {
      if (!sysPrin) return;
      const nextArray = (Array.isArray(nextArrayRaw) ? nextArrayRaw : []).map(normalizeVendorSliceItem);

      setClientList((prev) =>
        prev.map((client) => {
          if (!Array.isArray(client?.sysPrins)) return client;
          const nextSysPrins = client.sysPrins.map((sp) => {
            const spName = sp?.sysPrin ?? sp?.id?.sysPrin;
            if (spName === sysPrin) {
              return { ...sp, [sliceName]: nextArray };
            }
            return sp;
          });
          return { ...client, sysPrins: nextSysPrins };
        })
      );
    },
    [normalizeVendorSliceItem]
  );

  // Focused updaters passed to the editor window/tabs
  const onChangeVendorReceivedFrom = useCallback(
    (nextList) => {
      setSelectedData((prev) => ({
        ...(prev ?? {}),
        vendorReceivedFrom: (Array.isArray(nextList) ? nextList : []).map(normalizeVendorSliceItem),
      }));
      const sp = String(selectedData?.sysPrin ?? '');
      patchSysPrinSlice(sp, 'vendorReceivedFrom', nextList);
    },
    [patchSysPrinSlice, selectedData?.sysPrin, normalizeVendorSliceItem]
  );

  const onChangeVendorSentTo = useCallback(
    (nextList) => {
      setSelectedData((prev) => ({
        ...(prev ?? {}),
        vendorSentTo: (Array.isArray(nextList) ? nextList : []).map(normalizeVendorSliceItem),
      }));
      const sp = String(selectedData?.sysPrin ?? '');
      patchSysPrinSlice(sp, 'vendorSentTo', nextList);
    },
    [patchSysPrinSlice, selectedData?.sysPrin, normalizeVendorSliceItem]
  );

  // Force a clean remount of the SysPrin modal content when switching sysPrin
  const sysPrinKey = String(selectedData?.sysPrin ?? 'none');

  const [sysPrinsList, setSysPrinsList] = useState([]);

const onPatchSysPrinsList = useCallback((sysPrinCode, patch, clientOpt) => {
   if (!sysPrinCode) return;

   setClientList((prev) =>
     prev.map((c) => {
       const list = Array.isArray(c?.sysPrins) ? c.sysPrins : [];
       const nextSysPrins = list.map((sp) => {
         const code   = sp?.id?.sysPrin ?? sp?.sysPrin;
         const spClient = sp?.id?.client ?? sp?.client ?? c?.client ?? c?.billingSp;
         const match =
           code === sysPrinCode &&
           (clientOpt != null ? spClient === clientOpt : true);
         return match ? { ...sp, ...patch } : sp;
       });
       return list === nextSysPrins ? c : { ...c, sysPrins: nextSysPrins };
     })
   );

   // keep the right-hand pane reactive
   setSelectedData((prev) => (prev ? { ...prev, ...patch } : prev));
   setSelectedGroupRow((prev) => {
     if (!prev) return prev;
     const code   = prev?.id?.sysPrin ?? prev?.sysPrin;
     const pClient= prev?.id?.client  ?? prev?.client;
     const match =
       code === sysPrinCode &&
       (clientOpt != null ? pClient === clientOpt : true);
     return match ? { ...prev, ...patch } : prev;
   });
 }, []);

  return (
    <div className="d-flex flex-column" style={{ minHeight: '100vh', width: '80vw', overflow: 'visible' }}>
      {/* Input + Buttons */}
      <CRow className="px-3" style={{ marginBottom: '10px' }}>
        <CCol style={{ flex: '0 0 29%', maxWidth: '29%', paddingLeft: '0px', border: 'none' }}>
          <AutoCompleteInputBox
            inputValue={inputValue}
            setInputValue={setInputValue}
            onClientsFetched={handleClientsFetched}
            isWildcardMode={isWildcardMode}
            setIsWildcardMode={setIsWildcardMode}
            sx={{ '& .MuiOutlinedInput-root': { '& fieldset': { border: 'none' } } }}
          />
        </CCol>

        <CCol style={{ display: 'flex', alignItems: 'center', justifyContent: 'flex-start', border: 'none', maxWidth: '71%' }}>
          <p style={{ margin: 0, marginLeft: '20px', fontWeight: '800' }}>Client Information</p>
          <Button
            variant="outlined"
            onClick={() => setClientInformationWindow({ open: true, mode: 'delete' })}
            size="small"
            sx={{ fontSize: '0.78rem', marginLeft: 'auto', marginRight: '6px', textTransform: 'none' }}
            disabled={clientEditActionsDisabled}
          >
            Delete Client
          </Button>
          <Button
            variant="outlined"
            onClick={() => setClientInformationWindow({ open: true, mode: 'edit' })}
            size="small"
            sx={{ fontSize: '0.78rem', marginRight: '6px', textTransform: 'none' }}
            disabled={clientEditActionsDisabled}
          >
            Edit Client
          </Button>
          <Button
            variant="outlined"
            onClick={() => setClientInformationWindow({ open: true, mode: 'new' })}
            size="small"
            sx={{ fontSize: '0.78rem', textTransform: 'none' }}
          >
            New Client
          </Button>
        </CCol>
      </CRow>

      {/* Main Content */}
      <CRow style={{ flexGrow: 1, paddingLeft: '0px', paddingRight: '12px' }}>
        {/* Navigation Panel */}
        <CCol style={{ flex: '0 0 30%', maxWidth: '30%' }}>
          <CCard style={{ height: '100%' }}>
            <CCardBody style={{ height: '100%', padding: 0 }}>
              <div style={{ height: '1200px', overflow: 'hidden' }}>
                <NavigationPanel
                  onRowClick={handleRowClick}
                  clientList={clientList}
                  setClientList={setClientList}
                  currentPage={currentPage}
                  setCurrentPage={setCurrentPage}
                  isWildcardMode={isWildcardMode}
                  setIsWildcardMode={setIsWildcardMode}
                  onFetchWildcardPage={fetchWildcardPage}
                />
              </div>
            </CCardBody>
          </CCard>
        </CCol>

        {/* Client + SysPrin Info */}
        <CCol style={{ flex: '0 0 70%', maxWidth: '70%' }}>
          <CCard style={{ height: '100%' }}>
            <CCardBody style={{ height: '100%', padding: 0 }}>
              <div style={{ height: '1000px', overflow: 'hidden' }}>
                <CRow className="p-3" style={{ height: '400px' }}>
                  <CCol xs={12} style={{ height: '100%' }}>
                    <PreviewClientInformation
                      setClientInformationWindow={setClientInformationWindow}
                      selectedGroupRow={selectedGroupRow}
                    />
                  </CCol>
                </CRow>

                <CRow style={{ height: '30px', marginBottom: '10px' }}>
                  <CCol style={{ display: 'flex', alignItems: 'center', justifyContent: 'flex-start', maxWidth: '40%' }}>
                    <p style={{ margin: 0, marginLeft: '20px', fontWeight: '800' }}>SysPrin Information</p>
                  </CCol>
                  <CCol style={{ display: 'flex', alignItems: 'center', justifyContent: 'flex-start', maxWidth: '60%' }}>
                    <p style={{ margin: 0, marginLeft: '20px', fontWeight: '800' }}>
                      <Button
                        variant="outlined"
                        onClick={() => setSysPrinInformationWindow({ open: true, mode: 'changeAll' })}
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
                    </p>
                  </CCol>
                </CRow>

                <CRow className="px-3" style={{ marginBottom: '20px', height: '500px' }}>
                  <CCol xs={12} style={{ height: '100%' }}>
                    <CCard style={{ height: '100%' }}>
                      <CCardBody style={{ padding: '10px', height: '100%', overflowY: 'auto' }}>
                        <PreviewSysPrinInformation
                          setSysPrinInformationWindow={setSysPrinInformationWindow}
                          selectedData={selectedData}
                          selectedGroupRow={selectedGroupRow}
                        />
                      </CCardBody>
                    </CCard>
                  </CCol>
                </CRow>
              </div>
            </CCardBody>
          </CCard>
        </CCol>
      </CRow>

      {/* Drawers */}
      <Modal
        open={clientInformationWindow.open}
        onClose={() => setClientInformationWindow({ open: false, mode: 'edit' })}
        aria-labelledby="client-info-modal"
        aria-describedby="client-info-modal-description"
      >
        <Box
          sx={{
            position: 'absolute',
            top: '50%',
            left: '50%',
            transform: 'translate(-50%, -50%)',
            width: '860px',
            height: '680px',
            bgcolor: 'background.paper',
            boxShadow: 24,
            borderRadius: 2,
            p: 2,
            overflow: 'visible',
            maxHeight: 'none',
          }}
        >
          <ClientInformationWindow
            onClose={() => setClientInformationWindow({ open: false, mode: 'edit' })}
            selectedGroupRow={selectedGroupRow}
            setSelectedGroupRow={setSelectedGroupRow}
            mode={clientInformationWindow.mode}
          />
        </Box>
      </Modal>

      <Modal
        open={sysPrinInformationWindow.open}
        onClose={() => setSysPrinInformationWindow({ open: false, mode: 'edit' })}
        aria-labelledby="sysprin-info-modal"
        aria-describedby="sysprin-info-modal-description"
      >
        <Box
          sx={{
            position: 'absolute',
            top: '50%',
            left: '50%',
            transform: 'translate(-50%, -50%)',
            width: '960px',
            maxHeight: '90vh',
            bgcolor: 'background.paper',
            boxShadow: 24,
            borderRadius: 2,
            p: 2,
            overflowY: 'auto',
          }}
        >
          <SysPrinInformationWindow
            key={sysPrinKey}
            onClose={() => setSysPrinInformationWindow({ open: false, mode: 'edit' })}
            mode={sysPrinInformationWindow.mode}
            selectedData={selectedData}
            setSelectedData={setSelectedData}
            selectedGroupRow={selectedGroupRow}
            onChangeVendorReceivedFrom={onChangeVendorReceivedFrom}
            onChangeVendorSentTo={onChangeVendorSentTo}
            onPatchSysPrinsList={onPatchSysPrinsList} 
          />
        </Box>
      </Modal>
    </div>
  );
};

export default ClientInformationPage;




