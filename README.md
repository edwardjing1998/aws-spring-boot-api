import React, { useState, useEffect } from 'react';
import { Box, IconButton, Tabs, Tab, Button, TextField, Snackbar, Alert, AlertColor } from '@mui/material';
import CloseIcon from '@mui/icons-material/Close';
import { CRow, CCol } from '@coreui/react';

import EditClientInformation from '../EditClientInformation';
import EditAtmCashPrefix from '../atm-cash-prefix/EditAtmCashPrefix';
import EditClientReport from '../reports/EditClientReport';
import EditClientEmailSetup from '../emails/EditClientEmailSetup';

// Ideally, import these from the actual service file. 
// For now, assuming these return Promises resolving to the saved object.
import { handleCreateClient, handleUpdateClient, handleDeleteClient } from './ClientService';

// --- Interfaces ---

export interface ClientGroupRow {
  client: string;
  name: string;
  addr: string;
  city: string;
  state: string;
  zip: string;
  contact: string;
  phone: string;
  active: boolean;
  faxNumber: string;
  billingSp: string;
  reportBreakFlag: string | number;
  chLookUpType: string | number;
  excludeFromReport: boolean;
  positiveReports: boolean;
  subClientInd: boolean;
  subClientXref: string;
  amexIssued: boolean;
  clientEmail: any[];
  sysPrinsPrefixes: any[];
  reportOptions?: any[];
  sysPrins?: any[];
  [key: string]: any;
}

interface ClientInformationWindowProps {
  onClose: () => void;
  selectedGroupRow: ClientGroupRow | null;
  setSelectedGroupRow: React.Dispatch<React.SetStateAction<ClientGroupRow | null>>;
  mode: 'detail' | 'edit' | 'new' | 'delete';
  onClientCreated?: (client: ClientGroupRow) => void;
  onClientUpdated?: (client: ClientGroupRow) => void;
  onClientEmailsChanged?: (clientId: string, emails: any[]) => void;
  onClientDeleted?: (clientId: string) => void;
}

interface StatusState {
  open: boolean;
  severity: AlertColor;
  message: string;
}

// --- Helpers ---

// eslint-disable-next-line @typescript-eslint/no-unused-vars
const equalPrefixes = (a: any[] = [], b: any[] = []) =>
  a.length === b.length &&
  a.every((x, i) => {
    const y = (b[i] || {});
    return (
      String(x.billingSp ?? '') === String(y.billingSp ?? '') &&
      String(x.prefix ?? '') === String(y.prefix ?? '') &&
      String(x.atmCashRule ?? '') === String(y.atmCashRule ?? '')
    );
  });

// eslint-disable-next-line @typescript-eslint/no-unused-vars
const MAX = { client: 4, name: 30, addr: 35, city: 18 };

const ClientInformationWindow: React.FC<ClientInformationWindowProps> = ({
  onClose,
  selectedGroupRow,
  setSelectedGroupRow,
  mode,
  onClientCreated,
  onClientUpdated,
  onClientEmailsChanged,
  onClientDeleted
}) => {
  const [tabIndex, setTabIndex] = useState<number>(0);
  // eslint-disable-next-line @typescript-eslint/no-unused-vars
  const [isEditable, setIsEditable] = useState<boolean>(true);
  const [status, setStatus] = useState<StatusState>({ open: false, severity: 'info', message: '' });

  const makeEmptyClient = (): ClientGroupRow => ({
    client: '', name: '', addr: '', city: '', state: '', zip: '',
    contact: '', phone: '', active: false, faxNumber: '', billingSp: '',
    reportBreakFlag: '', chLookUpType: '', excludeFromReport: false, positiveReports: false,
    subClientInd: false, subClientXref: '', amexIssued: false,
    clientEmail: [],
    sysPrinsPrefixes: [],  // ensure default
  });

  useEffect(() => {
    if (mode === 'new') {
      setIsEditable(true);
      setSelectedGroupRow((makeEmptyClient()));
    } else if (mode === 'edit') {
      setIsEditable(true);
    } else {
      setIsEditable(false);
    }
  }, [mode, setSelectedGroupRow]);

  const viewRow: ClientGroupRow = mode === 'new'
    ? (selectedGroupRow ?? makeEmptyClient())
    : (selectedGroupRow ?? makeEmptyClient());

  const showStatus = (message: string, severity: AlertColor = 'success') =>
    setStatus({ open: true, severity, message });

  const handleStatusClose = (_e?: React.SyntheticEvent | Event, reason?: string) => {
    if (reason === 'clickaway') return;
    setStatus(prev => ({ ...prev, open: false }));
  };

  const onCreateClick = async () => {
    try {
      const saved = await handleCreateClient(viewRow);
      showStatus('Client created successfully', 'success');
      setSelectedGroupRow(prev => ({ ...(prev ?? {}), ...(saved ?? {}) }));
      onClientCreated?.(saved);
    } catch (err: any) {
      console.error(err);
      showStatus(err.message || 'An error occurred while creating.', 'error');
    }
  };

  const onUpdateClick = async () => {
    try {
      const raw = await handleUpdateClient(viewRow);
      const saved =
        raw && typeof raw === 'object'
          ? { ...viewRow, ...raw }
          : { ...viewRow, _message: typeof raw === 'string' ? raw : undefined };

      showStatus('Client updated successfully', 'success');
      setSelectedGroupRow(prev => ({ ...(prev ?? {}), ...(saved ?? {}) }));
      onClientUpdated?.(saved);
    } catch (err: any) {
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
      await handleDeleteClient(viewRow.client);
      showStatus('Client deleted successfully', 'success');
      // inform parent to remove it from the list
      onClientDeleted?.(viewRow.client);

     // clear local selection and close
     setSelectedGroupRow(null);
     onClose?.();
    } catch (err: any) {
      console.error(err);
      showStatus(err.message || 'An error occurred while deleting.', 'error');
    }
  };

  const onPrimaryClick = () => {
    if (mode === 'edit') return onUpdateClick();
    if (mode === 'delete') return onDeleteClick();
    return onCreateClick();
  };

  const handleEmailsChanged = (clientId: string, nextEmailList: any[]) => {
    if (!clientId) return;
    setSelectedGroupRow(prev => (prev ? { ...prev, client: clientId, clientEmail: Array.isArray(nextEmailList) ? nextEmailList : [] } : null));
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

        {mode === 'edit' && tabIndex === 0 && (      
          <Button variant="contained" size="small" color={mode === 'delete' ? 'error' : 'primary'} onClick={onPrimaryClick}>
            {tabIndex === 0 && (mode === 'new' ? 'Create' : mode === 'edit' ? 'Update' : mode === 'delete' ? 'Delete' : 'Save')}
          </Button>
         )}
        {mode === 'delete' && tabIndex === 3 && (      
          <Button variant="contained" size="small" color={mode === 'delete' ? 'error' : 'primary'} onClick={onPrimaryClick}>
            {tabIndex === 3 && (mode === 'new' ? 'Create' : mode === 'edit' ? 'Update' : mode === 'delete' ? 'Delete' : 'Save')}
          </Button>
         )} 
         {mode === 'new' && tabIndex === 0 && (      
          <Button variant="contained" size="small" color={mode === 'delete' ? 'error' : 'primary'} onClick={onPrimaryClick}>
            {tabIndex === 0 && (mode === 'new' ? 'Create' : mode === 'edit' ? 'Update' : mode === 'delete' ? 'Delete' : 'Save')}
          </Button>
         )}            
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

      <Box sx={{ p: 2, height: '800px', overflow: 'hidden', display: 'flex', flexDirection: 'column' }}>
        {/* Header inline inputs */}
        <Box sx={{ pb: 1, maxWidth: 900, mx: 'auto', width: '100%' }}>
          <CRow style={{ marginBottom: '12px', marginTop: 0 }}>
            <CCol xs={4}>
              <Box sx={{ display: 'flex', alignItems: 'center', gap: 1 }}>
                <Box component="label" sx={{ fontSize: '0.78rem', mr: 2, position: 'relative', top: -10 }}>Client ID</Box>
                <TextField
                  label=""
                  value={viewRow.client ?? ''}
                  size="small"
                  disabled={!isEditable}
                  sx={{ minWidth: 160, flex: 1, '& .MuiInputBase-root': { height: '30px', fontSize: '0.78rem' } }}
                  onChange={(e) =>
                    setSelectedGroupRow(prev => ({ ...(prev ?? makeEmptyClient()), client: e.target.value }))
                  }
                  inputProps={{ maxLength: MAX.client }}
                  helperText={`${viewRow.client.length}/${MAX.client}`}
                />
              </Box>
            </CCol>
            <CCol xs={6}>
              <Box sx={{ display: 'flex', alignItems: 'center' }}>
                <Box
                  component="label"
                  sx={{ fontSize: '0.78rem', mr: 2, position: 'relative', top: -10 }}
                >
                  Name
                </Box>
                <TextField
                  label=""
                  value={viewRow.name ?? ''}
                  size="small"
                  disabled={!isEditable}
                  sx={{ width: 240, '& .MuiInputBase-root': { height: '30px', fontSize: '0.78rem' } }}
                  onChange={(e) =>
                    setSelectedGroupRow(prev => ({ ...(prev ?? makeEmptyClient()), name: e.target.value }))
                  }
                  inputProps={{ maxLength: MAX.name }}
                  helperText={`${viewRow.name.length}/${MAX.name}`}
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
                    <EditClientReport
                      selectedGroupRow={viewRow}
                      isEditable={isEditable}
                      onDataChange={(nextSelectedGroupRow: any) => {
                        setSelectedGroupRow((prev) => {
                          if (!prev) return nextSelectedGroupRow;

                          const a = prev?.reportOptions ?? [];
                          const b = nextSelectedGroupRow?.reportOptions ?? [];

                          const changed =
                            a.length !== b.length ||
                            a.some((x: any, i: number) => {
                              const y = b[i] || {};
                              const xid = x.reportDetails?.reportId ?? x.reportId ?? null;
                              const yid = y.reportDetails?.reportId ?? y.reportId ?? null;
                              return (
                                (x.reportDetails?.queryName ?? '') !== (y.reportDetails?.queryName ?? '') ||
                                xid !== yid ||
                                !!x.receiveFlag !== !!y.receiveFlag ||
                                Number(x.outputTypeCd ?? 0) !== Number(y.outputTypeCd ?? 0) ||
                                Number(x.fileTypeCd ?? 0) !== Number(y.fileTypeCd ?? 0) ||
                                Number(x.emailFlag ?? 0) !== Number(y.emailFlag ?? 0) ||
                                (x.reportPasswordTx ?? '') !== (y.reportPasswordTx ?? '') ||
                                (x.emailBodyTx ?? '') !== (y.emailBodyTx ?? '')
                              );
                            });

                          if (!changed) return prev;
                          const merged = { ...prev, reportOptions: b };
                          onClientUpdated?.(merged);
                          return merged;
                        });
                      }}
                    />
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
                    <EditAtmCashPrefix
                      selectedGroupRow={viewRow}
                      onDataChange={(nextSelectedGroupRow: any) => {
                        setSelectedGroupRow((prev) => {
                          if (!prev) return nextSelectedGroupRow;
                          // eslint-disable-next-line @typescript-eslint/no-unused-vars
                          const a = prev?.sysPrinsPrefixes ?? [];
                          const b = nextSelectedGroupRow?.sysPrinsPrefixes ?? [];
                          // Note: You can re-enable equality check here if needed, 
                          // but for now relying on parent/child logic to avoid loops.
                          // if (equalPrefixes(a, b)) return prev; 
                          const merged = { ...prev, sysPrinsPrefixes: b };
                          onClientUpdated?.(merged);
                          return merged;
                        });
                      }}
                    />
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
