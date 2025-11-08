export const FlattenClientData = (clients, selectedClient, expandedGroups, isWildcardMode) => {
    const flattenedData = [];
  
    // 💡 Ensure clients is an array
    const clientsArray = Array.isArray(clients) ? clients : [];
  
    const clientsToShow = selectedClient === 'ALL'
      ? clientsArray
      : clientsArray.filter(c => c.client === selectedClient);
  
    clientsToShow.forEach(clientGroup => {
      const clientId = clientGroup.client;
      const isExpanded = expandedGroups[clientId] ?? false;
  
      flattenedData.push({
        isGroup: true,
        groupLevel: 1,
        groupLabel: (
          <span style={{ display: 'flex', alignItems: 'center', gap: '8px' }}>
            <span style={{
              display: 'inline-block',
              width: '18px',
              height: '18px',
              border: '1px solid #aaa',
              textAlign: 'center',
              fontSize: '12px',
              lineHeight: '16px',
              borderRadius: '3px',
              userSelect: 'none'
            }}>
              {isExpanded ? '−' : '+'}
            </span>
            <span>{`${clientId} - ${clientGroup.name}`}</span>
          </span>
        ),
        client: clientId,
        ...clientGroup,
        memoType: 'Pending',
      });
  
      if (isExpanded) {
        clientGroup.sysPrins?.forEach(sysPrin => {
          flattenedData.push({
            isGroup: false,
            client: clientId,
            sysPrin: sysPrin.sysPrin,
            name: clientGroup.name,
            address: clientGroup.addr,
            city: clientGroup.city,
            state: clientGroup.state,
            zip: clientGroup.zip,
            contact: clientGroup.contact,
            phone: clientGroup.phone,
            faxNumber: clientGroup.faxNumber,
            billingSp: clientGroup.billingSp,
            excludeFromReport: clientGroup.excludeFromReport,
            positiveReports: clientGroup.positiveReports,
            subClientInd: clientGroup.subClientInd,
            amexIssued: clientGroup.amexIssued,
            reportBreakFlag: clientGroup.reportBreakFlag,
            chLookUpType: clientGroup.chLookUpType,
            active: clientGroup.active,
            sysPrinActive: sysPrin?.sysPrinActive
          });
        });
      }
    });
  
    const clientGroupsOnly = flattenedData.filter(row => row.isGroup);
  
    const pagedGroups = isWildcardMode
      ? clientGroupsOnly
      : clientGroupsOnly.slice(0); // no pagination yet
  
    const visibleRows = [];
  
    pagedGroups.forEach(groupRow => {
      visibleRows.push(groupRow);
      if (expandedGroups[groupRow.client]) {
        const children = flattenedData.filter(row => !row.isGroup && row.client === groupRow.client);
        visibleRows.push(...children);
      }
    });
  
    return visibleRows;
  };




/**
 * Maps the raw data from the grid row and related lists into the full selectedData object.
 * Preserves locally edited slices when re-clicking the same sysPrin.
 */
export function mapRowDataToSelectedData(
  prev,
  rowData,
  atmCashPrefixes,
  clientEmails,
  reportOptions,
  sysPrinsList
) {
  const matchedSysPrin = sysPrinsList.find(
    sp => sp?.id?.sysPrin === rowData.sysPrin || sp?.sysPrin === rowData.sysPrin
  );

  const sameSysPrin = (prev?.sysPrin || '') === (rowData?.sysPrin || '');

  // Prefer locally edited slices if we're still on the same sysPrin
  const vendorReceivedFrom =
    sameSysPrin && Array.isArray(prev?.vendorReceivedFrom)
      ? prev.vendorReceivedFrom
      : matchedSysPrin?.vendorReceivedFrom || [];

  const vendorSentTo =
    sameSysPrin && Array.isArray(prev?.vendorSentTo)
      ? prev.vendorSentTo
      : matchedSysPrin?.vendorSentTo || [];

  // Keep prev when same sysPrin; otherwise use rowData ?? matched ?? ''
  const keep = (key) =>
    sameSysPrin && prev?.[key] !== undefined
      ? prev[key]
      : (rowData?.[key] ?? matchedSysPrin?.[key] ?? '');

  // Also preserve the entire sysPrins list when staying on the same sysPrin
  const mergedSysPrins =
    sameSysPrin && Array.isArray(prev?.sysPrins) ? prev.sysPrins : sysPrinsList;

  return {
    ...prev,
    ...rowData,

    sysPrinsPrefixes: atmCashPrefixes,
    clientEmail: clientEmails,
    reportOptions: reportOptions,

    // keep same key if we’re on the same one
    sysPrin: sameSysPrin ? (prev?.sysPrin ?? rowData.sysPrin ?? '') : (rowData.sysPrin || ''),

    // critical: don't lose locally patched list row
    sysPrins: mergedSysPrins,

    invalidDelivAreas: matchedSysPrin?.invalidDelivAreas || [],

    // keep your local edits when re-clicking the same sysPrin
    vendorReceivedFrom,
    vendorSentTo,

    // notes + ALL general fields
    notes: keep('notes'),

    // status letters now also use keep(...)
    statA: keep('statA'),
    statB: keep('statB'),
    statC: keep('statC'),
    statD: keep('statD'),
    statE: keep('statE'),
    statF: keep('statF'),
    statI: keep('statI'),
    statL: keep('statL'),
    statO: keep('statO'),
    statU: keep('statU'),
    statX: keep('statX'),
    statZ: keep('statZ'),

    // general dropdowns/flags (preserve prev on same sysPrin)
    special:           keep('special'),
    pinMailer:         keep('pinMailer'),
    destroyStatus:     keep('destroyStatus'),
    custType:          keep('custType'),
    returnStatus:      keep('returnStatus'),
    addrFlag:          keep('addrFlag'),
    astatRch:          keep('astatRch'),
    sysPrinActive:            keep('sysPrinActive'),
    nm13:              keep('nm13'),
    rps:               keep('rps'),
    tempAway:          keep('tempAway'),
    holdDays:          keep('holdDays'),
    tempAwayAtts:      keep('tempAwayAtts'),
    undeliverable:     keep('undeliverable'),
    forwardingAddress: keep('forwardingAddress'),
    nonUS:             keep('nonUS'),
    poBox:             keep('poBox'),
    badState:          keep('badState'),
    sysPrinContact:    keep('sysPrinContact'),
    sysPrinPhone:      keep('sysPrinPhone'),
  };
}

export const defaultSelectedData = {
  client: '',
  name: '',
  address: '',
  billingSp: '',
  atmCashRule: '',
  active: '',
  notes: '',
  special: '',
  pinMailer: '',
  destroyStatus: '',
  contact: '',
  phone: '',
  custType: '',
  returnStatus: '',
  addrFlag: '',
  astatRch: '',
  sysPrinActive: '',
  nm13: '',
  rps: '',
  sysPrinsPrefixes: [],
  clientEmail: [],
  reportOptions: [],
  sysPrins: [],
  sysPrin: '',
  invalidDelivAreas: [],
  vendorReceivedFrom: [],
  vendorSentTo: [],
  statA: '',
  statB: '',
  statC: '',
  statE: '',
  statF: '',
  statI: '',
  statL: '',
  statU: '',
  statD: '',
  statO: '',
  statX: '',
  statZ: '',
  sysPrinContact: '',
  sysPrinPhone: ''
};




import { Button, Typography } from '@mui/material';
import React, { useState, useEffect } from 'react';

const PAGE_SIZE = 12; // 4x4 grid
const COLUMNS = 4;

const PreviewClientSysPrinList = ({ data }) => {
  const [page, setPage] = useState(0);

  const pageCount = Math.ceil((data?.length || 0) / PAGE_SIZE);
  const pageData = data?.slice(page * PAGE_SIZE, (page + 1) * PAGE_SIZE) || [];

  const hasData = data && data.length > 0;

  const cellStyle = {
    backgroundColor: 'white',
    minHeight: '25px',
    display: 'flex',
    alignItems: 'center',
    justifyContent: 'flex-start',
    fontSize: '0.78rem',
    fontWeight: 150,
    padding: '0 10px',
    borderRadius: '0px',
    borderBottom: '1px dotted #ddd'
  };

  const headerStyle = {
    ...cellStyle,
    fontWeight: 'bold',
    backgroundColor: '#f0f0f0'
  };

  return (
    <div style={{ display: 'flex', flexDirection: 'column', height: '100%' }}>
      {/* Grid Table */}
      <div
        style={{
          flex: 1,
          display: 'grid',
          gridTemplateColumns: '1fr 1fr 1fr 1fr',
          rowGap: '0px',
          columnGap: '0px',
          minHeight: '200px',
          alignContent: 'start'
        }}
      >
        {/* Header Row */}
        <div style={headerStyle}>SysPrin</div>
        <div style={headerStyle}>Active</div>
        <div style={headerStyle}>SysPrin</div>
        <div style={headerStyle}>Active</div>

        {/* Data Rows */}
        {pageData.length > 0 ? (
          pageData.reduce((rows, item, index) => {
            if (index % 2 === 0) {
              const second = pageData[index + 1];
              rows.push(
                <React.Fragment key={index}>
                  <div style={cellStyle}>{item.sysPrin}</div>
                  <div style={cellStyle}>{item.sysprinActive ? 'Yes' : 'No'}</div>
                  <div style={cellStyle}>{second?.sysPrin || ''}</div>
                  <div style={cellStyle}>{second ? (second.sysprinActive ? 'Yes' : 'No') : ''}</div>
                </React.Fragment>
              );
            }
            return rows;
          }, [])
        ) : (
          <Typography sx={{ gridColumn: `span ${COLUMNS}`, fontSize: '0.75rem', padding: '0 16px' }}>
            xxxx - xxxx
          </Typography>
        )}
      </div>

      {/* Pagination */}
      <div
        style={{
          marginTop: '16px',
          display: 'flex',
          justifyContent: 'space-between',
          alignItems: 'center'
        }}
      >
        <Button
          variant="text"
          size="small"
          sx={{ fontSize: '0.7rem', padding: '2px 8px', minWidth: 'unset', textTransform: 'none' }}
          onClick={() => setPage((p) => Math.max(p - 1, 0))}
          disabled={!hasData || page === 0}
        >
          ◀ Previous
        </Button>

        <Typography fontSize="0.75rem">
          Page {hasData ? page + 1 : 0} of {hasData ? pageCount : 0}
        </Typography>

        <Button
          variant="text"
          size="small"
          sx={{ fontSize: '0.7rem', padding: '2px 8px', minWidth: 'unset', textTransform: 'none' }}
          onClick={() => setPage((p) => Math.min(p + 1, pageCount - 1))}
          disabled={!hasData || page === pageCount - 1}
        >
          Next ▶
        </Button>
      </div>
    </div>
  );
};

export default PreviewClientSysPrinList;



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






  
