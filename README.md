import React, { useState } from 'react';
import { Tabs, Tab, Box, SxProps, Theme } from '@mui/material';

// Ensure these paths match your project structure
import PreviewFilesReceivedFrom from '../vendor/PreviewFilesReceivedFrom';
import PreviewSysPrinNotes from '../components/PreviewSysPrinNotes';
import PreviewFilesSentTo from '../vendor/PreviewFilesSentTo';
import PreviewStatusOptions from '../options/PreviewStatusOptions';
import PreviewGeneralInformation from '../components/PreviewGeneralInformation';
import PreviewReMailOptions from '../options/PreviewReMailOptions';

// --- Interfaces ---

interface PreviewSysPrinInformationProps {
  setSysPrinInformationWindow?: (open: boolean) => void;
  selectedData?: any; // Replace 'any' with a more specific type if available (e.g., SysPrinData)
  selectedGroupRow?: any; // Replace 'any' with a more specific type if available (e.g., ClientGroupRow)
}

const PreviewSysPrinInformation: React.FC<PreviewSysPrinInformationProps> = ({ 
  setSysPrinInformationWindow, 
  selectedData, 
  // eslint-disable-next-line @typescript-eslint/no-unused-vars
  selectedGroupRow 
}) => {
  // eslint-disable-next-line @typescript-eslint/no-unused-vars
  const [isEditable] = useState(false);
  const [tabIndex, setTabIndex] = useState(0);

  const handleTabChange = (_event: React.SyntheticEvent, newValue: number) => {
    setTabIndex(newValue);
    if (newValue === 5 && typeof setSysPrinInformationWindow === 'function') {
      setSysPrinInformationWindow(true);
    }
  };

  const getStatusValue = (options: string[], code: string | number | undefined): string => {
    if (code === undefined || code === null) return '';
    const index = parseInt(String(code), 10);
    return !isNaN(index) ? options[index] || '' : '';
  };

  const sharedSx: SxProps<Theme> = {
    '& .MuiInputBase-root': {
      height: '20px',
      fontSize: '0.75rem'
    },
    '& .MuiInputBase-input': {
      padding: '4px 4px',
      height: '30px',
      fontSize: '0.75rem',
      lineHeight: '1rem'
    },
    '& .MuiInputLabel-root': {
      fontSize: '0.75rem',
      lineHeight: '1rem'
    },
    '& .MuiInputBase-input.Mui-disabled': {
      color: 'black',
      WebkitTextFillColor: 'black'
    },
    '& .MuiInputLabel-root.Mui-disabled': {
      color: 'black'
    }
  };

  const tabs = [
    { label: 'General Info' },
    { label: 'Status Options' },
    { label: 'Re-Mail Options' },
    { label: 'Files Sent To' },
    { label: 'Files Received From' },
    { label: 'Notes' }
  ];

  return (
    <>
      <Box sx={{ borderBottom: 1, borderColor: 'divider', marginBottom: '10px' }}>
        <Tabs value={tabIndex} onChange={handleTabChange}>
          {tabs.map((tab, index) => (
            <Tab
              key={index}
              label={tab.label}
              sx={{ textTransform: 'none' }} // 👈 removes uppercase
            />
          ))}
        </Tabs>
      </Box>

      {tabIndex === 0 && (
        <PreviewGeneralInformation
          selectedData={selectedData}
          // isEditable={isEditable} // isEditable not used in PreviewGeneralInformationProps
          sharedSx={sharedSx}
          // getStatusValue={getStatusValue} // getStatusValue not used in PreviewGeneralInformationProps based on previous file
        />
      )}

      {tabIndex === 1 && (
            <PreviewStatusOptions
              selectedData={selectedData}
              sharedSx={sharedSx}
              getStatusValue={getStatusValue}
            />
      )}


      {tabIndex === 2 && (
        <PreviewReMailOptions
          selectedData={selectedData}
          sharedSx={sharedSx}
        />
      )}

      {tabIndex === 3 && <PreviewFilesSentTo data={selectedData?.vendorSentTo || []} />}

      {tabIndex === 4 && <PreviewFilesReceivedFrom data={selectedData?.vendorReceivedFrom || []} />}

      {tabIndex === 5 && <PreviewSysPrinNotes data={selectedData || []} />}
    </>
  );
};

export default PreviewSysPrinInformation;


Type 'Dispatch<SetStateAction<{ open: boolean; mode: "edit" | "new" | "delete" | "changeAll" | "duplicate" | "move"; }>>' is not assignable to type '(open: boolean) => void'.
  Types of parameters 'value' and 'open' are incompatible.
    Type 'boolean' is not assignable to type 'SetStateAction<{ open: boolean; mode: "edit" | "new" | "delete" | "changeAll" | "duplicate" | "move"; }>'.ts(2322)
PreviewSysPrinInformation.tsx(15, 3): The expected type comes from property 'setSysPrinInformationWindow' which is declared here on type 'IntrinsicAttributes & PreviewSysPrinInformationProps'
(property) PreviewSysPrinInformationProps.setSysPrinInformationWindow?: ((open: boolean) => void) | undefined




import React, {
  useState,
  useEffect,
  useMemo,
  useCallback,
} from 'react';
import { CRow, CCol, CCard, CCardBody } from '@coreui/react';
import { Button, Modal, Box } from '@mui/material';

// Adjust imports to match your project structure
import ClientAutoCompleteInputBox from './components/ClientAutoCompleteInputBox';
import PreviewSysPrinInformation from './sys-prin-config/components/PreviewSysPrinInformation';
import PreviewClientInformation, { ClientGroupRow } from './components/PreviewClientInformation';
import {
  defaultSelectedData,
  mapRowDataToSelectedData,
} from './utils/SelectedData';
import NavigationPanel, {
  NavigationRow,
} from './utils/NavigationPanel';
import {
  fetchClientsPaging,
  fetchWildcardPage,
} from './components/ClientIntegrationService';

import ClientInformationWindow from './components/ClientInformationWindow';
import SysPrinInformationWindow from './sys-prin-config/SysPrinInformationWindow';

const ClientInformationPage: React.FC = () => {
  // Use ClientRow from NavigationPanel if imported, or redefine/import from shared types
  // Assuming ClientRow is compatible with ClientGroupRow or defined in NavigationPanel
  const [clientList, setClientList] = useState<any[]>([]); 
  const [currentPage, setCurrentPage] = useState<number>(0);
  const [inputValue, setInputValue] = useState<string>('');
  const [isWildcardMode, setIsWildcardMode] = useState<boolean>(false);

  // this is the "group row" or "client detail" object you pass around
  const [selectedGroupRow, setSelectedGroupRow] = useState<ClientGroupRow | null>(null);

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

  // State to force re-render/reset of NavigationPanel
  const [navPanelKey, setNavPanelKey] = useState<number>(0);

  // Logic to determine if a SysPrin is currently selected
  const isSysPrinSelected = !!selectedData && !!selectedData.sysPrin;
  const isClientSelected = !!selectedGroupRow && !!selectedGroupRow.client;

  // ---- fetch initial clients (paged) ----
  useEffect(() => {
    fetchClientsPaging(currentPage, 5)
      .then((data: any) => setClientList(Array.isArray(data) ? data : []))
      .catch((error: any) => {
        console.error('Error fetching clients:', error);
        alert(`Error fetching client details: ${error.message}`);
      });
  }, [currentPage]);

  // ---- map: client -> client record ----
  const clientMap = useMemo(() => {
    const map = new Map<string, any>();
    clientList.forEach((client) => {
      map.set(client.client, client);
    });
    return map;
  }, [clientList]);

  // ---- autocomplete callback replaces client list ----
  const handleClientsFetched = useCallback(
    (fetchedClients: any[] | unknown) => {
      const list = Array.isArray(fetchedClients) ? fetchedClients : [];
      setCurrentPage(0);
      setClientList((prev) => {
        const prevIds = prev.map((c) => c.client).join(',');
        const newIds = list.map((c: any) => c.client).join(',');
        return prevIds === newIds ? prev : list;
      });
    },
    [],
  );

  // ---- when user clicks rows in the nav grid ----
  const handleRowClick = useCallback(
    (rowData: NavigationRow) => {
      if (rowData.isGroup) {
        rowData.isGroup = false;
      }

      if (rowData.isGroup) {
        setClientEditActionsDisabled(false);
        setSelectedGroupRow(rowData as ClientGroupRow);
        // We do not set selectedData here; it's cleared by onClearSelectedData in NavPanel
        return;
      }

      const clientId = rowData.client || '';

      const matchedClient = clientMap.get(clientId);
      const atmCashPrefixes = matchedClient?.sysPrinsPrefixes || [];
      const clientEmails = matchedClient?.clientEmail || [];
      const reportOptions = matchedClient?.reportOptions || [];
      const sysPrinsList = matchedClient?.sysPrins || [];

      // If a child row is clicked, ensure the parent group row is also selected/retained
      if (!selectedGroupRow || selectedGroupRow.client !== clientId) {
         if (matchedClient) {
             setSelectedGroupRow(matchedClient);
             setClientEditActionsDisabled(false);
         }
      }

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
    [clientMap, selectedData, selectedGroupRow],
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
    (list: any[], saved: any | any): any[] => {
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

  // eslint-disable-next-line @typescript-eslint/no-unused-vars
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
          <ClientAutoCompleteInputBox
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
            onClick={() => {
              setClientInformationWindow({
                open: true,
                mode: 'new',
              });
              // Disable actions since we are creating new
              setClientEditActionsDisabled(true);
              // Clear selectedData when creating new client
              setSelectedData(defaultSelectedData);
              
              // NEW: Close currently open group/details in Nav Panel by resetting selection and re-mounting nav
              setSelectedGroupRow(null);
              setNavPanelKey((prev) => prev + 1);
            }}
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
                {/* Added key prop to force re-mount on New Client click */}
                <NavigationPanel
                  key={navPanelKey}
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
                        disabled={!isSysPrinSelected}
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
                        disabled={!isSysPrinSelected}
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
                        disabled={!isSysPrinSelected}
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
                        disabled={!isClientSelected || isSysPrinSelected}
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
                        disabled={!isSysPrinSelected}
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
                        disabled={!isSysPrinSelected}
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




