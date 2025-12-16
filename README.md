import React, { useState } from 'react';
import { CRow, CCol, CCard, CCardBody } from '@coreui/react';
import { Tabs, Tab, Box, TextField, FormControl, FormControlLabel, Checkbox } from '@mui/material';
import PreviewClientEmails from './emails/PreviewClientEmails';
import PreviewAtmAndCashPrefix from './atm-cash-prefix/PreviewAtmAndCashPrefix';
import PreviewClientReports from './reports/PreviewClientReports';
import PreviewClientSysPrinList from './PreviewClientSysPrinList';
import { REPORT_BREAK_OPTIONS, SEARCH_TYPE_OPTIONS } from './utils/FieldValueMapping';

// --- Interfaces ---

export interface ClientGroupRow {
  client?: string;
  name?: string;
  addr?: string;
  city?: string;
  state?: string;
  zip?: string;
  contact?: string;
  phone?: string;
  phoneCountryCode?: string;
  active?: boolean;
  faxNumber?: string;
  billingSp?: string;
  reportBreakFlag?: string | number;
  chLookUpType?: string | number;
  excludeFromReport?: boolean;
  positiveReports?: boolean;
  subClientInd?: boolean;
  subClientXref?: string;
  amexIssued?: boolean;
  sysPrins?: any[]; // You can refine this type based on SysPrinDTO
  sysPrinTotal?: number;
  reportOptions?: any[]; // You can refine based on ClientReportItem
  reportOptionTotal?: number;
  sysPrinsPrefixes?: any[]; // You can refine based on AtmCashPrefixRow
  clientPrefixTotal?: number;
  clientEmail?: any[];
  clientEmailTotal?: number;
  [key: string]: any;
}

interface PreviewClientInformationProps {
  setClientInformationWindow?: (value: boolean) => void;
  selectedGroupRow: ClientGroupRow | null;
}

const PreviewClientInformation: React.FC<PreviewClientInformationProps> = ({
  setClientInformationWindow,
  selectedGroupRow,
}) => {
  // eslint-disable-next-line @typescript-eslint/no-unused-vars
  const [isEditable] = useState<boolean>(false);
  const [tabIndex, setTabIndex] = useState<number>(0);

  const handleTabChange = (_event: React.SyntheticEvent, newValue: number) => {
    setTabIndex(newValue);
  };

  const sharedSx = {
    '& .MuiInputBase-root': {
      height: '38px',       // outer container
      fontSize: '0.78rem',
    },
    '& .MuiInputBase-input': {
      padding: '8px 10px',  // more padding for taller field
      height: '22px',       // input line height
      fontSize: '0.78rem',
      lineHeight: '1.2rem',
    },
    '& .MuiInputLabel-root': {
      fontSize: '0.78rem',
      lineHeight: '1.2rem',
    },
    '& .MuiInputBase-input.Mui-disabled': {
      color: 'black',
      WebkitTextFillColor: 'black',
    },
    '& .MuiInputLabel-root.Mui-disabled': {
      color: 'black',
    },
  };
  
  return (
    <>
      <Box sx={{ borderBottom: 1, borderColor: 'divider', marginBottom: '10px' }}>
        <Tabs value={tabIndex} onChange={handleTabChange} variant="scrollable" scrollButtons="auto">
          <Tab label="General Info" sx={{ textTransform: 'none' }} />
          <Tab label="Sys/Prin List" sx={{ textTransform: 'none' }} />
          <Tab label="Client Reports" sx={{ textTransform: 'none' }} />
          <Tab label="ATM Prefixes" sx={{ textTransform: 'none' }} />
          <Tab label="Client Emails" sx={{ textTransform: 'none' }} />
        </Tabs>
      </Box>

      {tabIndex === 0 && (
        <>
          <CCard style={{ height: '70px', marginBottom: '4px', marginTop: '15px', boxShadow: 'none', border: 'none', backgroundColor: 'white' }}>
            <CCardBody
              style={{
                padding: '0.25rem 0.5rem',
                height: '100%',
                backgroundColor: 'white',
                display: 'flex',
                flexDirection: 'column',
                justifyContent: 'space-between',
              }}
            >
            <CRow style={{ height: '35px' }}>
              <CCol style={{ flex: '0 0 25%' }}>
                <p style={{ margin: 0, fontSize: '0.78rem' }}>Phone</p>
              </CCol>
              <CCol style={{ flex: '0 0 25%' }}>
                <p style={{ margin: 0, fontSize: '0.78rem'  }}>Fax Number</p>
              </CCol>
              <CCol style={{ flex: '0 0 25%' }}>
                <p style={{ margin: 0, fontSize: '0.78rem' }}>Contact</p>
              </CCol>

              <CCol style={{ flex: '0 0 3%' }}>
              </CCol>

              {/* Address column, right-aligned */}
              <CCol
                style={{
                  textAlign: 'left', 
                  display: 'flex',
                  flexDirection: 'column',
                  justifyContent: 'center',
                  flex: '0 0 20%' 
                }}
              >
                {/* Address row – allow wrapping */}
                <CRow style={{ minHeight: '20px' }}>
                  <CCol
                    style={{
                      flex: '0 0 100%',
                      fontSize: '0.78rem',
                      textAlign: 'left',
                      whiteSpace: 'normal',      // allow wrapping
                      wordBreak: 'break-word',   // break long words if needed
                      lineHeight: '1.1rem',
                      color: '#2554C7'
                    }}
                  >
                    {selectedGroupRow?.addr || 'xxx'}
                  </CCol>
                </CRow>

                {/* City / State row – can be fixed height or also minHeight if you want wrapping */}
                <CRow style={{ height: '20px' }}>
                  <CCol
                    style={{
                      flex: '0 0 100%',
                      fontSize: '0.78rem',
                      textAlign: 'left',
                      whiteSpace: 'nowrap',      // keep on one line (optional)
                      overflow: 'hidden',
                      textOverflow: 'ellipsis',
                      color: '#2554C7'
                    }}
                  >
                    {selectedGroupRow?.city || 'xxx'}, {selectedGroupRow?.state || 'xx'}
                  </CCol>
                </CRow>

                {/* Zip row with bottom border – stays properly aligned */}
                <CRow style={{ height: '20px', borderBottom: '1px solid #2554C7' }}>
                  <CCol
                    style={{
                      flex: '0 0 100%',
                      fontSize: '0.78rem',
                      textAlign: 'left',
                      color: '#2554C7'
                    }}
                  >
                    {selectedGroupRow?.zip || 'xxx'}
                  </CCol>
                </CRow>
              </CCol>
              <CCol style={{ flex: '0 0 2%', fontSize: '0.78rem', textAlign: 'left' }}>
              </CCol>
            </CRow>

              <CRow style={{ height: '35px' }}>
                <CCol>
                  <TextField placeholder="xxx" value={selectedGroupRow?.phone || 'xxx'} size="small" fullWidth disabled={!isEditable} sx={sharedSx} />
                </CCol>
                <CCol>
                   <TextField placeholder="xxx" value={selectedGroupRow?.faxNumber || 'xxx'} size="small" fullWidth disabled={!isEditable} sx={sharedSx} />
                </CCol>
                <CCol>
                   <TextField placeholder="xxx" value={selectedGroupRow?.contact || 'xxx'} size="small" fullWidth disabled={!isEditable} sx={sharedSx} />
                </CCol>
                <CCol>
                </CCol>
              </CRow>
            </CCardBody>
          </CCard>

          <CCard style={{ height: '70px', marginBottom: '4px', marginTop: '15px', boxShadow: 'none', border: 'none', backgroundColor: 'white' }}>
            <CCardBody
              style={{
                padding: '0.25rem 0.5rem',
                height: '100%',
                backgroundColor: 'white',
                display: 'flex',
                flexDirection: 'column',
                justifyContent: 'space-between',
              }}
            >
              <CRow style={{ height: '35px' }}>
                <CCol><p style={{ margin: 0, fontSize: '0.78rem' }}>Billing Sys/Prin</p></CCol>
                <CCol><p style={{ margin: 0, fontSize: '0.78rem' }}>Report Breaks</p></CCol>
                <CCol><p style={{ margin: 0, fontSize: '0.78rem' }}>Search Type</p></CCol>
                <CCol><p style={{ margin: 0, fontSize: '0.78rem' }}>Client ID XRef</p></CCol>
              </CRow>

              <CRow style={{ height: '35px' }}>
                <CCol>
                  <TextField placeholder="xxx" value={selectedGroupRow?.billingSp || ''} size="small" fullWidth disabled={!isEditable} sx={sharedSx} />
                </CCol>
                <CCol>
                  <TextField
                    placeholder="xxx"
                    value={REPORT_BREAK_OPTIONS.find(opt => opt.value === String(selectedGroupRow?.reportBreakFlag ?? ''))?.label || ''}
                    size="small"
                    fullWidth
                    disabled={!isEditable}
                    sx={sharedSx}
                  />
                </CCol>
                <CCol>
                  <TextField
                    placeholder="xxx"
                    value={SEARCH_TYPE_OPTIONS.find(opt => opt.value === String(selectedGroupRow?.chLookUpType ?? ''))?.label || ''}
                    size="small"
                    fullWidth
                    disabled={!isEditable}
                    sx={sharedSx}
                  />
                </CCol>
                <CCol>
                  <TextField placeholder="xxx" value={selectedGroupRow?.subClientXref || ''} size="small" fullWidth disabled={!isEditable} sx={sharedSx} />
                </CCol>
              </CRow>
            </CCardBody>
          </CCard>

          <CCard style={{ marginTop: '25px', marginBottom: '10px' }}>
            <CCardBody
              style={{
                padding: '0.8rem',
                backgroundColor: 'white',
                display: 'flex',
                flexDirection: 'column',
                rowGap: '0px',
              }}
            >
              <CRow style={{ height: '35px' }}>
                <CCol style={{ flex: '0 0 40%' }}>
                  <FormControlLabel
                    control={<Checkbox size="small" checked={selectedGroupRow?.active || false} disabled={!isEditable} />}
                    label="Client Active"
                    sx={{ pl: 1, m: 0, '& .MuiFormControlLabel-label': { fontSize: '0.78rem', color: 'black' } }}
                  />
                </CCol>
                <CCol style={{ flex: '0 0 40%' }}>
                  <FormControlLabel
                    control={<Checkbox size="small" checked={selectedGroupRow?.positiveReports || false} disabled={!isEditable} />}
                    label="Positive Reporting"
                    sx={{ pl: 1, m: 0, '& .MuiFormControlLabel-label': { fontSize: '0.78rem', color: 'gray' } }}
                  />
                </CCol>
                <CCol style={{ flex: '0 0 20%', fontSize: '0.78rem' }}>
                </CCol>
              </CRow>

              <CRow style={{ height: '35px' }}>

                <CCol style={{ flex: '0 0 40%' }}>
                  <FormControlLabel
                    control={<Checkbox size="small" checked={selectedGroupRow?.excludeFromReport || false} disabled={!isEditable} />}
                    label="Exclude From Postage Reports"
                    sx={{ pl: 1, m: 0, '& .MuiFormControlLabel-label': { fontSize: '0.78rem', color: 'black' } }}
                  />
                </CCol>

                <CCol style={{ flex: '0 0 40%' }}>
                  <FormControlLabel
                    control={<Checkbox size="small" checked={selectedGroupRow?.subClientInd || false} disabled={!isEditable} />}
                    label="Sub Client"
                    sx={{ pl: 1, m: 0, '& .MuiFormControlLabel-label': { fontSize: '0.78rem', color: 'black' } }}
                  />
                </CCol>
              </CRow>

              <CRow style={{ height: '35px' }}>
                <CCol style={{ flex: '0 0 40%' }}>
                  <FormControlLabel
                    control={<Checkbox size="small" checked={selectedGroupRow?.amexIssued || false} disabled={!isEditable} />}
                    label="Check Here: If American Express Issued"
                    sx={{ pl: 1, m: 0, '& .MuiFormControlLabel-label': { fontSize: '0.78rem', color: 'black' } }}
                  />
                </CCol>
                <CCol style={{ flex: '0 0 30%' }}>
                </CCol>
              </CRow>
            </CCardBody>
          </CCard>
        </>
      )}

      {tabIndex === 1 && (
        <>
         <CCard
            style={{
              height: '35px',
              marginBottom: '4px',
              marginTop: '2px',
              border: 'none',                   // remove border
              backgroundColor: '#f3f6f8',       // very light green background
              boxShadow: 'none',                // remove any shadow
              borderRadius: '4px'               // optional: slight rounding
            }}
          >
            <CCardBody
              className="d-flex align-items-center"
              style={{
                padding: '0.25rem 0.5rem',
                height: '100%',
                backgroundColor: 'transparent' // ensure it inherits from card
              }}
            >
              <p
                style={{
                  margin: 0,
                  fontSize: '0.78rem',
                  fontWeight: '500'
                }}
              >
                {selectedGroupRow
                  ? `Selected Client: ${selectedGroupRow.client} - ${selectedGroupRow.name || ''}`
                  : 'No Client Found'}
              </p>
            </CCardBody>
          </CCard>

          <CCard style={{ height: '250px', marginBottom: '4px' }}>
            <CCardBody style={{ padding: '0.25rem 0.5rem' }}>
              <PreviewClientSysPrinList 
              data={selectedGroupRow?.sysPrins || []} 
              sysPrinTotal={selectedGroupRow?.sysPrinTotal} />
            </CCardBody>
          </CCard>
        </>
      )}

      {tabIndex === 2 && (
        <>
         <CCard
            style={{
              height: '35px',
              marginBottom: '4px',
              marginTop: '2px',
              border: 'none',                   // remove border
              backgroundColor: '#f3f6f8',       // very light green background
              boxShadow: 'none',                // remove any shadow
              borderRadius: '4px'               // optional: slight rounding
            }}
          >
            <CCardBody
              className="d-flex align-items-center"
              style={{
                padding: '0.25rem 0.5rem',
                height: '100%',
                backgroundColor: 'transparent' // ensure it inherits from card
              }}
            >
              <p
                style={{
                  margin: 0,
                  fontSize: '0.78rem',
                  fontWeight: '500'
                }}
              >
                {selectedGroupRow
                  ? `Selected Client: ${selectedGroupRow.client} - ${selectedGroupRow.name || ''}`
                  : 'No Client Found'}
              </p>
            </CCardBody>
          </CCard>

          <CCard style={{ height: '250px', marginBottom: '4px' }}>
            <CCardBody style={{ padding: '0.25rem 0.5rem' }}>
              <PreviewClientReports 
                data={selectedGroupRow?.reportOptions || []} 
                reportOptionTotal={selectedGroupRow?.reportOptionTotal} 
              />
            </CCardBody>
          </CCard>
        </>
      )}

      {tabIndex === 3 && (
        <>
         <CCard
            style={{
              height: '35px',
              marginBottom: '4px',
              marginTop: '2px',
              border: 'none',                   // remove border
              backgroundColor: '#f3f6f8',       // very light green background
              boxShadow: 'none',                // remove any shadow
              borderRadius: '4px'               // optional: slight rounding
            }}
          >
            <CCardBody
              className="d-flex align-items-center"
              style={{
                padding: '0.25rem 0.5rem',
                height: '100%',
                backgroundColor: 'transparent' // ensure it inherits from card
              }}
            >
              <p
                style={{
                  margin: 0,
                  fontSize: '0.78rem',
                  fontWeight: '500'
                }}
              >
                {selectedGroupRow
                  ? `Selected Client: ${selectedGroupRow.client} - ${selectedGroupRow.name || ''}`
                  : 'No Client Found'}
              </p>
            </CCardBody>
          </CCard>

          <CCard style={{ height: '250px', marginBottom: '4px' }}>
            <CCardBody style={{ padding: '0.25rem 0.5rem' }}>
              <PreviewAtmAndCashPrefix 
                data={selectedGroupRow?.sysPrinsPrefixes || []} 
                clientPrefixTotal={selectedGroupRow?.clientPrefixTotal} />
            </CCardBody>
          </CCard>
        </>
      )}

      {tabIndex === 4 && (
        <>
         <CCard
            style={{
              height: '35px',
              marginBottom: '4px',
              marginTop: '2px',
              border: 'none',                   // remove border
              backgroundColor: '#f3f6f8',       // very light green background
              boxShadow: 'none',                // remove any shadow
              borderRadius: '4px'               // optional: slight rounding
            }}
          >
            <CCardBody
              className="d-flex align-items-center"
              style={{
                padding: '0.25rem 0.5rem',
                height: '100%',
                backgroundColor: 'transparent' // ensure it inherits from card
              }}
            >
              <p
                style={{
                  margin: 0,
                  fontSize: '0.78rem',
                  fontWeight: '500'
                }}
              >
                {selectedGroupRow
                  ? `Selected Client: ${selectedGroupRow.client} - ${selectedGroupRow.name || ''}`
                  : 'No Client Found'}
              </p>
            </CCardBody>
          </CCard>

          <CCard style={{ height: '250px', marginBottom: '4px' }}>
            <CCardBody style={{ padding: '0.25rem 0.5rem' }}>
              <PreviewClientEmails 
                data={selectedGroupRow?.clientEmail || []} 
                clientEmailTotal={selectedGroupRow?.clientEmailTotal} />
            </CCardBody>
          </CCard>
        </>
      )}
    </>
  );
};

export default PreviewClientInformation;



import React, {
  useState,
  useEffect,
  useMemo,
  useCallback,
} from 'react';
import { CRow, CCol, CCard, CCardBody } from '@coreui/react';
import { Button, Modal, Box } from '@mui/material';

import AutoCompleteInputBox from '../../../components/ClientAutoCompleteInputBox';
import PreviewSysPrinInformation from './sys-prin-config/PreviewSysPrinInformation';
import PreviewClientInformation from './PreviewClientInformation';
import {
  defaultSelectedData,
  mapRowDataToSelectedData,
} from './utils/SelectedData';
import NavigationPanel, {
  ClientRow,
  NavigationRow,
} from './NavigationPanel';
import {
  fetchClientsPaging,
  fetchWildcardPage,
} from './utils/ClientIntegrationService';

import ClientInformationWindow from './utils/ClientInformationWindow';
import SysPrinInformationWindow from './utils/SysPrinInformationWindow';

const ClientInformationPage: React.FC = () => {
  const [clientList, setClientList] = useState<ClientRow[]>([]);
  const [currentPage, setCurrentPage] = useState<number>(0);
  const [inputValue, setInputValue] = useState<string>('');
  const [isWildcardMode, setIsWildcardMode] = useState<boolean>(false);

  // this is the "group row" or "client detail" object you pass around
  const [selectedGroupRow, setSelectedGroupRow] = useState<any>(null);

  const [selectedData, setSelectedData] =
    useState<typeof defaultSelectedData>(defaultSelectedData);

  const [clientInformationWindow, setClientInformationWindow] = useState<{
    open: boolean;
    mode: 'edit' | 'new' | 'delete';
  }>({ open: false, mode: 'edit' });

  const [sysPrinInformationWindow, setSysPrinInformationWindow] = useState<{
    open: boolean;
    mode: 'edit' | 'new' | 'delete' | 'changeAll' | 'duplicate' | 'move';
  }>({ open: false, mode: 'edit' });

  const [clientEditActionsDisabled, setClientEditActionsDisabled] =
    useState<boolean>(true);

  // ---- fetch initial clients (paged) ----
  useEffect(() => {
    fetchClientsPaging(currentPage, 5)
      .then((data) => setClientList(Array.isArray(data) ? data : []))
      .catch((error: any) => {
        console.error('Error fetching clients:', error);
        alert(`Error fetching client details: ${error.message}`);
      });
  }, [currentPage]);

  // ---- map: client -> client record ----
  const clientMap = useMemo(() => {
    const map = new Map<string, ClientRow>();
    clientList.forEach((client) => {
      map.set(client.client, client);
    });
    return map;
  }, [clientList]);

  // ---- autocomplete callback replaces client list ----
  const handleClientsFetched = useCallback(
    (fetchedClients: ClientRow[] | unknown) => {
      const list = Array.isArray(fetchedClients) ? fetchedClients : [];
      setCurrentPage(0);
      setClientList((prev) => {
        const prevIds = prev.map((c) => c.client).join(',');
        const newIds = list.map((c) => c.client).join(',');
        return prevIds === newIds ? prev : list;
      });
    },
    [],
  );

  // ---- when user clicks rows in the nav grid ----
  const handleRowClick = useCallback(
    (rowData: NavigationRow) => {
      if (rowData.isGroup) {
        setClientEditActionsDisabled(false);
        setSelectedGroupRow(rowData);
        return;
      }

      const clientId = rowData.client || '';

      const matchedClient = clientMap.get(clientId);
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
        sysPrinsList,
      );
      setSelectedData(mappedData);
    },
    [clientMap, selectedData],
  );

  // when a client is deleted from the modal
  const handleClientDeleted = useCallback((deletedId?: string | number) => {
    if (!deletedId) return;
    setClientList((prev) =>
      prev.filter(
        (c) => String(c.client) !== String(deletedId),
      ),
    );
    setSelectedGroupRow((prev: any) =>
      prev && String(prev.client) === String(deletedId) ? null : prev,
    );
    setSelectedData((prev) =>
      prev && String((prev as any).client) === String(deletedId)
        ? defaultSelectedData
        : prev,
    );
  }, []);

  // =========================
  // Helpers for syncing edits
  // =========================

  // Upsert a client into clientList by client id
  const upsertClient = useCallback(
    (list: ClientRow[], saved: ClientRow | any): ClientRow[] => {
      if (!saved || !saved.client) return list;
      const idx = list.findIndex((c) => c.client === saved.client);
      if (idx >= 0) {
        const copy = [...list];
        copy[idx] = { ...copy[idx], ...saved };
        return copy;
      }
      // Insert new client at top; adjust as needed
      return [saved, ...list];
    },
    [],
  );

  // when autocomplete returns detail JSON (from /sysprins/detail/..)
  const handleClientDetailLoaded = useCallback(
    (detail: any) => {
      if (!detail) return;

      // 1) upsert client into left-side list
      setClientList((prev) => upsertClient(prev, detail));

      // 2) set as selected group row and enable edit buttons
      setSelectedGroupRow(detail);
      setClientEditActionsDisabled(false);

      // 3) build selectedData based on first sysPrin (if any)
      const sysPrinsList = Array.isArray(detail.sysPrins)
        ? detail.sysPrins
        : [];
      const atmCashPrefixes = detail.sysPrinsPrefixes || [];
      const clientEmails = detail.clientEmail || [];
      const reportOptions = detail.reportOptions || [];

      if (sysPrinsList.length > 0) {
        const firstSysPrinRow = sysPrinsList[0];
        setSelectedData((prev) =>
          mapRowDataToSelectedData(
            (prev ?? defaultSelectedData) as any,
            firstSysPrinRow,
            atmCashPrefixes,
            clientEmails,
            reportOptions,
            sysPrinsList,
          ),
        );
      } else {
        setSelectedData(defaultSelectedData);
      }
    },
    [upsertClient],
  );

  // Normalize a vendor record into the parent "canonical" shape
  const normalizeVendorSliceItem = useCallback(
    (v: any) => {
      const id = String(
        v?.vendorId ??
          v?.vendId ??
          v?.vendor?.vendId ??
          v?.vendor?.id ??
          '',
      );
      const name =
        v?.vendName ??
        v?.vendorName ??
        v?.vendor?.vendNm ??
        v?.vendor?.name ??
        String(id);
      const q =
        typeof (v?.queueForMail ??
          v?.queForMail ??
          v?.queForMailCd) === 'string'
          ? ['1', 'Y', 'TRUE'].includes(
              String(
                v?.queueForMail ??
                  v?.queForMail ??
                  v?.queForMailCd,
              ).toUpperCase(),
            )
          : !!(v?.queueForMail ?? v?.queForMail);

      const sysPrin = String((selectedData as any)?.sysPrin ?? '');

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
    [(selectedData as any)?.sysPrin],
  );

  // Patch the matching sysPrin object inside clientList (source-of-truth used by handleRowClick)
  const patchSysPrinSlice = useCallback(
    (sysPrin: string, sliceName: string, nextArrayRaw: any) => {
      if (!sysPrin) return;
      const nextArray = (Array.isArray(nextArrayRaw)
        ? nextArrayRaw
        : []
      ).map(normalizeVendorSliceItem);

      setClientList((prev) =>
        prev.map((client) => {
          if (!Array.isArray(client?.sysPrins)) return client;
          const nextSysPrins = client.sysPrins.map((sp: any) => {
            const spName = sp?.sysPrin ?? sp?.id?.sysPrin;
            if (spName === sysPrin) {
              return { ...sp, [sliceName]: nextArray };
            }
            return sp;
          });
          return { ...client, sysPrins: nextSysPrins };
        }),
      );
    },
    [normalizeVendorSliceItem],
  );

  // Focused updaters passed to the editor window/tabs
  const onChangeVendorReceivedFrom = useCallback(
    (nextList: any[]) => {
      setSelectedData((prev) => ({
        ...(prev ?? {}) as any,
        vendorReceivedFrom: (Array.isArray(nextList)
          ? nextList
          : []
        ).map(normalizeVendorSliceItem),
      }));
      const sp = String((selectedData as any)?.sysPrin ?? '');
      patchSysPrinSlice(sp, 'vendorReceivedFrom', nextList);
    },
    [patchSysPrinSlice, (selectedData as any)?.sysPrin, normalizeVendorSliceItem],
  );

  const onChangeVendorSentTo = useCallback(
    (nextList: any[]) => {
      setSelectedData((prev) => ({
        ...(prev ?? {}) as any,
        vendorSentTo: (Array.isArray(nextList)
          ? nextList
          : []
        ).map(normalizeVendorSliceItem),
      }));
      const sp = String((selectedData as any)?.sysPrin ?? '');
      patchSysPrinSlice(sp, 'vendorSentTo', nextList);
    },
    [patchSysPrinSlice, (selectedData as any)?.sysPrin, normalizeVendorSliceItem],
  );

  // apply email list changes from the modal to both clientList and selectedGroupRow
  const handleClientEmailsChanged = useCallback(
    (clientId: string, nextEmailList: any[]) => {
      if (!clientId) return;

      setClientList((prev) =>
        prev.map((c) =>
          c.client === clientId
            ? {
                ...c,
                clientEmail: Array.isArray(nextEmailList)
                  ? nextEmailList
                  : [],
              }
            : c,
        ),
      );

      setSelectedGroupRow((prev: any) =>
        prev?.client === clientId
          ? {
              ...(prev ?? {}),
              clientEmail: Array.isArray(nextEmailList)
                ? nextEmailList
                : [],
            }
          : prev,
      );
    },
    [],
  );

  // Force a clean remount of the SysPrin modal content when switching sysPrin
  const sysPrinKey = String((selectedData as any)?.sysPrin ?? 'none');

  const [sysPrinsList, setSysPrinsList] = useState<any[]>([]);

  const onPatchSysPrinsList = useCallback(
    (sysPrin: string, patch: any, clientId?: string) => {
      const isRemove = !!patch?.__REMOVE__;

      const makeRow = (existing: any = {}) => {
        const base = isRemove ? existing : { ...existing, ...patch };
        const idObj = base.id ?? {};
        return {
          ...base,
          client: clientId ?? base.client ?? idObj.client,
          sysPrin:
            sysPrin ?? base.sysPrin ?? idObj.sysPrin,
          id: {
            client: clientId ?? idObj.client,
            sysPrin: sysPrin ?? idObj.sysPrin,
          },
        };
      };

      // 1) Update clientList[*].sysPrins
      setClientList((prev) =>
        prev.map((c) => {
          if (
            String(c?.client ?? '') !==
            String(clientId ?? '')
          )
            return c;
          let arr = Array.isArray(c.sysPrins)
            ? [...c.sysPrins]
            : [];
          const idx = arr.findIndex(
            (sp: any) =>
              (sp?.id?.sysPrin ?? sp?.sysPrin) === sysPrin,
          );

          if (isRemove) {
            if (idx >= 0) arr.splice(idx, 1);
          } else {
            if (idx >= 0) arr[idx] = makeRow(arr[idx]);
            else arr.push(makeRow());
          }
          return { ...c, sysPrins: arr };
        }),
      );

      // 2) Update selectedGroupRow.sysPrins if it belongs to that client
      setSelectedGroupRow((prev: any) => {
        if (
          !prev ||
          String(prev?.client ?? '') !==
            String(clientId ?? '')
        )
          return prev;
        let arr = Array.isArray(prev.sysPrins)
          ? [...prev.sysPrins]
          : [];
        const idx = arr.findIndex(
          (sp: any) =>
            (sp?.id?.sysPrin ?? sp?.sysPrin) === sysPrin,
        );

        if (isRemove) {
          if (idx >= 0) arr.splice(idx, 1);
        } else {
          if (idx >= 0) arr[idx] = makeRow(arr[idx]);
          else arr.push(makeRow());
        }
        return { ...prev, sysPrins: arr };
      });

      // 3) Update local cache
      setSysPrinsList((prev) => {
        const list = Array.isArray(prev) ? [...prev] : [];
        const idx = list.findIndex(
          (sp: any) =>
            (sp?.id?.sysPrin ?? sp?.sysPrin) === sysPrin &&
            (clientId
              ? (sp?.id?.client ?? sp?.client) === clientId
              : true),
        );
        if (isRemove) {
          if (idx >= 0) list.splice(idx, 1);
        } else {
          if (idx >= 0) list[idx] = makeRow(list[idx]);
          else list.push(makeRow());
        }
        return list;
      });
    },
    [],
  );

  // handlers to receive created/updated client from the window
  const handleClientCreated = useCallback(
    (saved: any) => {
      if (!saved) return;
      setClientList((prev) => upsertClient(prev, saved));
      // focus the newly created row in the right pane
      setSelectedGroupRow(saved);
      // optionally switch the window to edit mode after creation
      setClientInformationWindow({
        open: true,
        mode: 'edit',
      });
    },
    [upsertClient],
  );

  const handleClientUpdated = useCallback(
    (saved: any) => {
      if (!saved) return;
      setClientList((prev) => upsertClient(prev, saved));
      setSelectedGroupRow((prev: any) => ({
        ...(prev ?? {}),
        ...(saved ?? {}),
      }));
    },
    [upsertClient],
  );

  return (
    <div
      className="d-flex flex-column"
      style={{
        minHeight: '100vh',
        width: '80vw',
        overflow: 'visible',
      }}
    >
      {/* Input + Buttons */}
      <CRow className="px-3" style={{ marginBottom: '10px' }}>
        <CCol
          style={{
            flex: '0 0 29%',
            maxWidth: '29%',
            paddingLeft: '0px',
            border: 'none',
          }}
        >
          <AutoCompleteInputBox
            inputValue={inputValue}
            setInputValue={setInputValue}
            onClientsFetched={handleClientsFetched}
            isWildcardMode={isWildcardMode}
            setIsWildcardMode={setIsWildcardMode}
            // when a client is selected and detail JSON is loaded
            onClientDetailLoaded={handleClientDetailLoaded}
          />
        </CCol>

        <CCol
          style={{
            display: 'flex',
            alignItems: 'center',
            justifyContent: 'flex-start',
            border: 'none',
            maxWidth: '71%',
          }}
        >
          <p
            style={{
              margin: 0,
              marginLeft: '20px',
              fontWeight: 800,
            }}
          >
            Client Info
          </p>
          <Button
            variant="outlined"
            onClick={() =>
              setClientInformationWindow({
                open: true,
                mode: 'delete',
              })
            }
            size="small"
            sx={{
              fontSize: '0.78rem',
              marginLeft: 'auto',
              marginRight: '6px',
              textTransform: 'none',
            }}
            disabled={clientEditActionsDisabled}
          >
            Delete Client
          </Button>
          <Button
            variant="outlined"
            onClick={() =>
              setClientInformationWindow({
                open: true,
                mode: 'edit',
              })
            }
            size="small"
            sx={{
              fontSize: '0.78rem',
              marginRight: '6px',
              textTransform: 'none',
            }}
            disabled={clientEditActionsDisabled}
          >
            Edit Client
          </Button>
          <Button
            variant="outlined"
            onClick={() =>
              setClientInformationWindow({
                open: true,
                mode: 'new',
              })
            }
            size="small"
            sx={{
              fontSize: '0.78rem',
              textTransform: 'none',
            }}
          >
            New Client
          </Button>
        </CCol>
      </CRow>

      {/* Main Content */}
      <CRow
        style={{
          flexGrow: 1,
          paddingLeft: '0px',
          paddingRight: '12px',
        }}
      >
        {/* Navigation Panel */}
        <CCol style={{ flex: '0 0 30%', maxWidth: '30%' }}>
          <CCard style={{ height: '100%' }}>
            <CCardBody
              style={{ height: '100%', padding: 0 }}
            >
              <div
                style={{
                  height: '1200px',
                  overflow: 'hidden',
                }}
              >
                <NavigationPanel
                  onRowClick={handleRowClick}
                  clientList={clientList}
                  setClientList={setClientList}
                  currentPage={currentPage}
                  setCurrentPage={setCurrentPage}
                  isWildcardMode={isWildcardMode}
                  setIsWildcardMode={setIsWildcardMode}
                  onFetchWildcardPage={fetchWildcardPage}
                  // ⬇️ NEW: clear selectedData when a group is expanded
                  onClearSelectedData={() => setSelectedData(defaultSelectedData)}
                />
              </div>
            </CCardBody>
          </CCard>
        </CCol>

        {/* Client + SysPrin Info */}
        <CCol style={{ flex: '0 0 70%', maxWidth: '70%' }}>
          <CCard style={{ height: '100%' }}>
            <CCardBody
              style={{ height: '100%', padding: 0 }}
            >
              <div
                style={{
                  height: '1200px',
                  overflow: 'hidden',
                }}
              >
                <CRow
                  className="p-3"
                  style={{ height: '400px' }}
                >
                  <CCol
                    xs={12}
                    style={{ height: '100%' }}
                  >
                    <PreviewClientInformation
                      setClientInformationWindow={
                        setClientInformationWindow
                      }
                      selectedGroupRow={selectedGroupRow}
                    />
                  </CCol>
                </CRow>

                <CRow
                  className="p-3"
                  style={{ height: '50px' }}
                >
                  <CCol
                    xs={12}
                    style={{ height: '100%' }}
                  />
                </CRow>

                <CRow
                  style={{
                    height: '30px',
                    marginBottom: '10px',
                  }}
                >
                  <CCol
                    style={{
                      display: 'flex',
                      alignItems: 'center',
                      justifyContent: 'flex-start',
                      maxWidth: '20%',
                    }}
                  >
                    <p
                      style={{
                        margin: 0,
                        marginLeft: '20px',
                        fontWeight: 800,
                      }}
                    >
                      SysPrin Info
                    </p>
                  </CCol>
                  <CCol
                    style={{
                      display: 'flex',
                      alignItems: 'center',
                      justifyContent: 'flex-start',
                      maxWidth: '80%',
                    }}
                  >
                    <p
                      style={{
                        margin: 0,
                        marginLeft: '20px',
                        fontWeight: 800,
                      }}
                    >
                      <Button
                        variant="outlined"
                        onClick={() =>
                          setSysPrinInformationWindow({
                            open: true,
                            mode: 'changeAll',
                          })
                        }
                        size="small"
                        sx={{
                          fontSize: '0.78rem',
                          marginRight: '6px',
                          textTransform: 'none',
                        }}
                      >
                        Change All
                      </Button>
                      <Button
                        variant="outlined"
                        onClick={() =>
                          setSysPrinInformationWindow({
                            open: true,
                            mode: 'delete',
                          })
                        }
                        size="small"
                        sx={{
                          fontSize: '0.78rem',
                          marginRight: '6px',
                          textTransform: 'none',
                        }}
                      >
                        Delete SysPrin
                      </Button>
                      <Button
                        variant="outlined"
                        onClick={() =>
                          setSysPrinInformationWindow({
                            open: true,
                            mode: 'edit',
                          })
                        }
                        size="small"
                        sx={{
                          fontSize: '0.78rem',
                          textTransform: 'none',
                          marginRight: '6px',
                        }}
                      >
                        Edit SysPrin
                      </Button>
                      <Button
                        variant="outlined"
                        onClick={() =>
                          setSysPrinInformationWindow({
                            open: true,
                            mode: 'new',
                          })
                        }
                        size="small"
                        sx={{
                          fontSize: '0.78rem',
                          marginRight: '6px',
                          textTransform: 'none',
                        }}
                      >
                        New SysPrin
                      </Button>
                      <Button
                        variant="outlined"
                        onClick={() =>
                          setSysPrinInformationWindow({
                            open: true,
                            mode: 'duplicate',
                          })
                        }
                        size="small"
                        sx={{
                          fontSize: '0.78rem',
                          marginRight: '6px',
                          textTransform: 'none',
                        }}
                      >
                        Duplicate
                      </Button>
                      <Button
                        variant="outlined"
                        onClick={() =>
                          setSysPrinInformationWindow({
                            open: true,
                            mode: 'move',
                          })
                        }
                        size="small"
                        sx={{
                          fontSize: '0.78rem',
                          marginRight: '6px',
                          textTransform: 'none',
                        }}
                      >
                        Move
                      </Button>
                    </p>
                  </CCol>
                </CRow>

                <CRow
                  className="px-3"
                  style={{
                    marginBottom: '20px',
                    height: '500px',
                  }}
                >
                  <CCol
                    xs={12}
                    style={{ height: '100%' }}
                  >
                    <CCard style={{ height: '100%' }}>
                      <CCardBody
                        style={{
                          padding: '10px',
                          height: '100%',
                          overflowY: 'auto',
                        }}
                      >
                        <PreviewSysPrinInformation
                          setSysPrinInformationWindow={
                            setSysPrinInformationWindow
                          }
                          selectedData={selectedData}
                          selectedGroupRow={
                            selectedGroupRow
                          }
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

      {/* Client modal */}
      <Modal
        open={clientInformationWindow.open}
        onClose={() =>
          setClientInformationWindow({
            open: false,
            mode: 'edit',
          })
        }
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
            height: '750px',
            bgcolor: 'background.paper',
            boxShadow: 24,
            borderRadius: 2,
            p: 2,
            overflow: 'visible',
            maxHeight: '95vh',
          }}
        >
          <ClientInformationWindow
            onClose={() =>
              setClientInformationWindow({
                open: false,
                mode: 'edit',
              })
            }
            selectedGroupRow={selectedGroupRow}
            setSelectedGroupRow={setSelectedGroupRow}
            mode={clientInformationWindow.mode}
            onClientCreated={handleClientCreated}
            onClientUpdated={handleClientUpdated}
            onClientEmailsChanged={handleClientEmailsChanged}
            onClientDeleted={handleClientDeleted}
          />
        </Box>
      </Modal>

      {/* SysPrin modal */}
      <Modal
        open={sysPrinInformationWindow.open}
        onClose={() =>
          setSysPrinInformationWindow({
            open: false,
            mode: 'edit',
          })
        }
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
            onClose={() =>
              setSysPrinInformationWindow({
                open: false,
                mode: 'edit',
              })
            }
            mode={sysPrinInformationWindow.mode}
            selectedData={selectedData}
            setSelectedData={setSelectedData}
            selectedGroupRow={selectedGroupRow}
            setSelectedGroupRow={setSelectedGroupRow}
            onChangeVendorReceivedFrom={
              onChangeVendorReceivedFrom
            }
            onChangeVendorSentTo={onChangeVendorSentTo}
            onPatchSysPrinsList={onPatchSysPrinsList}
          />
        </Box>
      </Modal>
    </div>
  );
};

export default ClientInformationPage;


Type 'Dispatch<SetStateAction<{ open: boolean; mode: "edit" | "new" | "delete"; }>>' is not assignable to type '(value: boolean) => void'.
  Types of parameters 'value' and 'value' are incompatible.
    Type 'boolean' is not assignable to type 'SetStateAction<{ open: boolean; mode: "edit" | "new" | "delete"; }>'.ts(2322)
PreviewClientInformation.tsx(44, 3): The expected type comes from property 'setClientInformationWindow' which is declared here on type 'IntrinsicAttributes & PreviewClientInformationProps'




