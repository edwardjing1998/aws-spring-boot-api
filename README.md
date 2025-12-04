// src/views/sys-prin-configuration/NavigationPanel.tsx

import React, {
  useEffect,
  useRef,
  useState,
  useMemo,
} from 'react';
import { CCard, CCol, CRow } from '@coreui/react';
import {
  ModuleRegistry,
  ClientSideRowModelModule,
  ColDef,
  RowClassRules,
  GridApi,
  RowClickedEvent,
} from 'ag-grid-community';
import { AgGridReact } from 'ag-grid-react';
import '../../../scss/sys-prin-configuration/client-information.scss';
import { FlattenClientData } from './utils/FlattenClientData';
import {
  fetchClientsByPage,
  resetClientListService,
} from './utils/ClientIntegrationService';
import {
  fetchSysPrinsByClientPaged,
  SysPrinDTO,
} from './utils/SysPrinIntegrationService';

ModuleRegistry.registerModules([ClientSideRowModelModule]);

// ---- Types ----

export interface ClientRow {
  client: string;
  name?: string;
  sysPrins?: SysPrinDTO[];   // ⬅️ SysPrins live here per client
  [key: string]: any;
}

export interface NavigationRow {
  client: string;
  name?: string;
  sysPrin?: string;
  isGroup?: boolean;
  groupLevel?: number;
  groupLabel?: React.ReactNode;
  isPagerRow?: boolean;      // ⬅️ special row for SysPrin pager buttons
  [key: string]: any;
}

interface NavigationPanelProps {
  onRowClick?: (row: NavigationRow) => void;
  clientList: ClientRow[];
  setClientList: React.Dispatch<React.SetStateAction<ClientRow[]>>;
  currentPage: number;
  setCurrentPage: React.Dispatch<React.SetStateAction<number>>;
  isWildcardMode: boolean;
  setIsWildcardMode: React.Dispatch<React.SetStateAction<boolean>>;
  onFetchWildcardPage?: (page: number) => void;
  onFetchGroupDetails?: (clientId: string) => void;
  onClearSelectedData?: () => void;
}

const NavigationPanel: React.FC<NavigationPanelProps> = ({
  onRowClick,
  clientList,
  setClientList,
  currentPage,
  setCurrentPage,
  isWildcardMode,
  setIsWildcardMode,
  onFetchWildcardPage,
  onFetchGroupDetails,
  onClearSelectedData,
}) => {
  const [selectedClient, setSelectedClient] = useState<string>('ALL');
  const [tableData, setTableData] = useState<NavigationRow[]>([]);
  const [pageSize] = useState<number>(5);
  const [expandedGroups, setExpandedGroups] = useState<Record<string, boolean>>({});
  const gridApiRef = useRef<GridApi<NavigationRow> | null>(null);

  // per-client SysPrin page index from backend (0-based)
  const [sysPrinPageByClient, setSysPrinPageByClient] = useState<Record<string, number>>({});
  // We also track total elements per client to enable "Last Page" calculation
  const [sysPrinTotalByClient, setSysPrinTotalByClient] = useState<Record<string, number>>({});

  // Ref Pattern: Keep refs in sync with state to avoid Stale Closures in AG Grid renderers
  const sysPrinPageRef = useRef(sysPrinPageByClient);
  const sysPrinTotalRef = useRef(sysPrinTotalByClient);

  // Update refs whenever state changes
  sysPrinPageRef.current = sysPrinPageByClient;
  sysPrinTotalRef.current = sysPrinTotalByClient;

  const SYS_PRIN_PAGE_SIZE = 10; // what you pass as &size=

  const buttonStyle: React.CSSProperties = {
    border: 'none',
    background: 'none',
    padding: '6px 12px',
    cursor: 'pointer',
    fontSize: '0.85rem',
    color: '#555',
    whiteSpace: 'nowrap',
  };

  // Initialize expandedGroups whenever clientList changes
  useEffect(() => {
    setExpandedGroups((prev) => {
      const next: Record<string, boolean> = {};
      clientList.forEach((client) => {
        next[client.client] = prev[client.client] ?? false;
      });
      return next;
    });
  }, [clientList]);

  // Optional: reset SysPrin page counters when client list changes
  useEffect(() => {
    setSysPrinPageByClient({});
    setSysPrinTotalByClient({});
  }, [clientList]);

  const flattenedData = useMemo(() => {
    // FlattenClientData is now expected to use client.sysPrins directly.
    const rows = FlattenClientData(
      clientList,
      selectedClient,
      expandedGroups,
      isWildcardMode,
      sysPrinPageByClient,
      SYS_PRIN_PAGE_SIZE
    ) as NavigationRow[];

    // de-dupe by (client, type, sysPrin)
    const seen = new Set<string>();
    const uniq: NavigationRow[] = [];

    for (const r of rows) {
      if (r.isGroup) {
        const key = `g:${r.client}`;
        if (!seen.has(key)) {
          seen.add(key);
          uniq.push(r);
        }
        continue;
      }

      if (r.isPagerRow) {
        const key = `p:${r.client}`;
        if (!seen.has(key)) {
          seen.add(key);
          uniq.push(r);
        }
        continue;
      }

      const key = `s:${r.client}:${r.sysPrin}`;
      if (!seen.has(key)) {
        seen.add(key);
        uniq.push(r);
      }
    }

    return uniq;
  }, [clientList, selectedClient, expandedGroups, isWildcardMode, sysPrinPageByClient, onClearSelectedData]);

  useEffect(() => {
    setTableData(flattenedData);
  }, [flattenedData]);

  // ---- bottom client list pager ----
  const goToNextPage = async () => {
    const nextPage = currentPage + 1;
    if (isWildcardMode && typeof onFetchWildcardPage === 'function') {
      onFetchWildcardPage(nextPage);
    } else {
      try {
        const data = await fetchClientsByPage(nextPage, pageSize);
        setClientList([]);
        setClientList(data);
        setCurrentPage(nextPage);
      } catch (error: any) {
        console.error('Error fetching clients:', error);
        alert(`Error fetching client details: ${error.message}`);
      }
    }
  };

  const goToPreviousPage = async () => {
    const previousPage = Math.max(0, currentPage - 1);
    if (isWildcardMode && typeof onFetchWildcardPage === 'function') {
      onFetchWildcardPage(previousPage);
    } else {
      try {
        const data = await fetchClientsByPage(previousPage, pageSize);
        setClientList([]);
        setClientList(data);
        setCurrentPage(previousPage);
      } catch (error: any) {
        console.error('Error fetching clients:', error);
        alert(`Error fetching client details: ${error.message}`);
      }
    }
  };

  const resetClientList = async () => {
    try {
      const data = await resetClientListService(pageSize);
      setClientList([]);
      setIsWildcardMode(false);
      setClientList(data);
      setCurrentPage(0);
    } catch (error) {
      console.error('Reset fetch failed:', error);
    }
  };

  // ---- Grid columns ----
  // Wrap columnDefs in useMemo so it updates when sysPrinPageByClient changes.
  const columnDefs = useMemo<ColDef<NavigationRow>[]>(() => [
    {
      field: 'groupLabel',
      headerName: 'Clients',
      colSpan: (params) => (params.data?.isGroup ? 2 : 1),
      cellRenderer: (params: any) => {
        const row = params.data as NavigationRow | undefined;
        if (!row?.isGroup) return '';
        return <span>{row.groupLabel}</span>;
      },
      valueGetter: (params) =>
        params.data?.isGroup ? `${params.data.client} - ${params.data.name ?? ''}` : '',
      flex: 0.5,
      minWidth: 80,
    },
    {
      field: 'sysPrin',
      headerName: 'Sys Prin',
      width: 200,
      minWidth: 200,
      flex: 2,
      cellRenderer: (params: any) => {
        const row = params.data as NavigationRow | undefined;
        if (!row) return '';

        // group row: nothing in SysPrin column
        if (row.isGroup) return '';

        const clientId = row.client;
        // This const is useful for rendering the text "Page X", 
        // BUT NOT for calculation inside async handlers due to stale closures.
        const displayPage = sysPrinPageByClient[clientId] ?? 0;

        // ---- Pager row: call paged REST API and update parent clientList ----
        if (row.isPagerRow) {
          
          // Shared helper to update state from API response
          const handleApiResponse = (resp: any, targetPage: number) => {
             // Replace sysPrins array for this client in parent state
             setClientList((prev) =>
              prev.map((c) =>
                c.client === clientId ? { ...c, sysPrins: resp.items } : c,
              ),
            );

            // Update page index
            setSysPrinPageByClient((prev) => ({
              ...prev,
              [clientId]: targetPage,
            }));

            // Update total count if available
            if (typeof resp.total === 'number') {
              setSysPrinTotalByClient((prev) => ({
                ...prev,
                [clientId]: resp.total
              }));
            }
          };

          const handleSysPrinFirst = async (
            e: React.MouseEvent<HTMLButtonElement>,
          ) => {
            e.stopPropagation();
            // Read fresh state from Ref
            const freshPage = sysPrinPageRef.current[clientId] ?? 0;
            if (freshPage === 0) return; 

            try {
              const resp = await fetchSysPrinsByClientPaged(
                clientId,
                0, // First page
                SYS_PRIN_PAGE_SIZE,
              );
              handleApiResponse(resp, 0);
            } catch (err: any) {
              console.error('Error fetching first SysPrins', err);
              alert(`Error fetching first page: ${err.message}`);
            }
          };

          const handleSysPrinPrev = async (
            e: React.MouseEvent<HTMLButtonElement>,
          ) => {
            e.stopPropagation();
            // Read fresh state from Ref to avoid stale closure
            const freshPage = sysPrinPageRef.current[clientId] ?? 0;
            const targetPage = Math.max(0, freshPage - 1);
            
            if (targetPage === freshPage) return;

            try {
              const resp = await fetchSysPrinsByClientPaged(
                clientId,
                targetPage,
                SYS_PRIN_PAGE_SIZE,
              );
              handleApiResponse(resp, targetPage);
            } catch (err: any) {
              console.error('Error fetching previous SysPrins', err);
              alert(`Error fetching prev page: ${err.message}`);
            }
          };

          const handleSysPrinNext = async (
            e: React.MouseEvent<HTMLButtonElement>,
          ) => {
            e.stopPropagation();
            // Read fresh state from Ref to avoid stale closure
            const freshPage = sysPrinPageRef.current[clientId] ?? 0;
            const targetPage = freshPage + 1;

            try {
              const resp = await fetchSysPrinsByClientPaged(
                clientId,
                targetPage,
                SYS_PRIN_PAGE_SIZE,
              );

              const totalPages = resp.size > 0
                  ? Math.max(1, Math.ceil(resp.total / resp.size))
                  : 1;

              if (resp.items.length === 0 && targetPage >= totalPages) {
                 return; // No more data
              }
              handleApiResponse(resp, targetPage);
            } catch (err: any) {
              console.error('Error fetching next SysPrins', err);
              alert(`Error fetching next page: ${err.message}`);
            }
          };

          const handleSysPrinLast = async (
            e: React.MouseEvent<HTMLButtonElement>,
          ) => {
            e.stopPropagation();
            
            // Read fresh state from Ref
            const freshPage = sysPrinPageRef.current[clientId] ?? 0;
            let total = sysPrinTotalRef.current[clientId];
            
            if (total === undefined) {
               try {
                 const resp = await fetchSysPrinsByClientPaged(clientId, freshPage, SYS_PRIN_PAGE_SIZE);
                 total = resp.total;
                 setSysPrinTotalByClient(prev => ({...prev, [clientId]: total}));
               } catch(err: any) {
                 console.error('Error determining last page', err);
                 return;
               }
            }

            const lastPage = Math.max(0, Math.ceil(total / SYS_PRIN_PAGE_SIZE) - 1);
            
            if (freshPage === lastPage) return; 

            try {
              const resp = await fetchSysPrinsByClientPaged(
                clientId,
                lastPage,
                SYS_PRIN_PAGE_SIZE,
              );
              handleApiResponse(resp, lastPage);
            } catch (err: any) {
              console.error('Error fetching last SysPrins', err);
              alert(`Error fetching last page: ${err.message}`);
            }
          };

          return (
            <div
              style={{
                display: 'flex',
                justifyContent: 'flex-start',
                alignItems: 'center',
                gap: '4px',
                width: '100%',
              }}
            >
              {/* First Page Button */}
              <button
                type="button"
                onClick={handleSysPrinFirst}
                style={{
                  border: '0px',
                  borderRadius: '4px',
                  padding: '0 4px',
                  fontSize: '0.75rem',
                  lineHeight: '1.1',
                  background: '#fff',
                  cursor: 'pointer',
                  height: '22px',
                  color: 'blue',
                }}
                title="First Page"
              >
                ⏮
              </button>

              <button
                type="button"
                onClick={handleSysPrinPrev}
                style={{
                  border: '0px',
                  borderRadius: '4px',
                  padding: '0 4px',
                  fontSize: '0.75rem',
                  lineHeight: '1.1',
                  background: '#fff',
                  cursor: 'pointer',
                  height: '22px',
                  color: 'blue',
                }}
              >
                ◀ Prev
              </button>

              <span style={{ fontSize: '0.75rem', color: '#666' }}>
                Page {displayPage + 1}
              </span>

              <button
                type="button"
                onClick={handleSysPrinNext}
                style={{
                  border: '0px',
                  borderRadius: '4px',
                  padding: '0 4px',
                  fontSize: '0.75rem',
                  lineHeight: '1.1',
                  background: '#fff',
                  cursor: 'pointer',
                  height: '22px',
                  color: 'blue',
                }}
              >
                Next ▶
              </button>

              {/* Last Page Button */}
              <button
                type="button"
                onClick={handleSysPrinLast}
                style={{
                  border: '0px',
                  borderRadius: '4px',
                  padding: '0 4px',
                  fontSize: '0.75rem',
                  lineHeight: '1.1',
                  background: '#fff',
                  cursor: 'pointer',
                  height: '22px',
                  color: 'blue',
                }}
                title="Last Page"
              >
                ⏭
              </button>
            </div>
          );
        }

        // normal SysPrin rows
        return (
          <span>
            <span role="img" aria-label="gear" style={{ marginRight: '6px' }}>
              ⚙️
            </span>
            {params.value}
          </span>
        );
      },
      valueGetter: (params) =>
        params.data?.isGroup || params.data?.isPagerRow ? '' : params.data?.sysPrin ?? '',
    },
  ], [sysPrinPageByClient, sysPrinTotalByClient]); // Add dependencies here

  const defaultColDef: ColDef<NavigationRow> = {
    flex: 1,
    resizable: true,
    minWidth: 120,
    sortable: false,
    filter: false,
    floatingFilter: false,
  };

  const rowClassRules: RowClassRules<NavigationRow> = {
    'client-group-row': (params) =>
      !!params.data?.isGroup && params.data?.groupLevel === 1,
  };

  const handleRowClicked = (event: RowClickedEvent<NavigationRow>) => {
  const row = event.data;
  if (!row) return;
  const clientId = row.client;

  // avoid reacting to clicks on pager rows
  if (row.isPagerRow) {
    return;
  }

  setTimeout(() => {
    if (row.isGroup && clientId) {
      setExpandedGroups((prev) => {
        const currentlyExpanded = prev[clientId] ?? false;
        const willExpand = !currentlyExpanded;

        // ✅ Only clear when transitioning from collapsed -> expanded
        if (!currentlyExpanded && willExpand && typeof onClearSelectedData === 'function') {
          onClearSelectedData();
        }

        const newState: Record<string, boolean> = {};
        clientList.forEach((c) => {
          newState[c.client] = false;
        });
        newState[clientId] = willExpand;
        return newState;
      });

      // reset sysPrin page when switching group
      setSysPrinPageByClient((prev) => ({
        ...prev,
        [clientId]: 0,
      }));

      setTimeout(() => {
        if (onRowClick) onRowClick({ ...row });
        if (onFetchGroupDetails) onFetchGroupDetails(clientId);
      }, 0);
    } else if (!row.isGroup && clientId) {
      // ✅ Clicking a sysPrin row will NO LONGER clear selectedData
      setTimeout(() => {
        if (onRowClick) onRowClick(row);
      }, 0);
    }
  }, 0);
};

  return (
    <div className="d-flex flex-column h-100">
      <CRow className="flex-grow-1">
        <CCol xs={12} className="d-flex flex-column h-100">
          <CCard
            className="flex-grow-1 d-flex flex-column"
            style={{
              height: '1200px',
              border: 'none',
              boxShadow: 'none',
              overflow: 'hidden',
            }}
          >
            <div style={{ flex: 1, overflow: 'hidden' }}>
              <div
                className="ag-grid-container ag-theme-quartz no-grid-border"
                style={{
                  height: '100%',
                  width: '100%',
                  overflowY: 'auto',
                  overflowX: 'hidden',
                }}
              >
                <AgGridReact<NavigationRow>
                  rowData={tableData}
                  columnDefs={columnDefs}
                  defaultColDef={defaultColDef}
                  rowClassRules={rowClassRules}
                  pagination={false}
                  suppressScrollOnNewData={true}
                  animateRows={true}
                  onGridReady={(params) => {
                    gridApiRef.current = params.api;
                  }}
                  onRowClicked={handleRowClicked}
                  getRowId={(params) => {
                    const d = params.data as NavigationRow;
                    if (d?.isGroup) return `g:${d.client}`;
                    if (d?.isPagerRow) return `p:${d.client}`;
                    return `s:${d.client}:${d.sysPrin}`;
                  }}
                />
              </div>
            </div>

            {/* bottom client pager */}
            <div
              style={{
                padding: '4px',
                background: '#fafafa',
                display: 'flex',
                justifyContent: 'center',
                alignItems: 'center',
                columnGap: '4px',
                flexWrap: 'nowrap',
                overflowX: 'hidden',
              }}
            >
              {!isWildcardMode ? (
                <div
                  style={{
                    padding: '4px',
                    textAlign: 'center',
                    background: '#fafafa',
                    display: 'flex',
                    justifyContent: 'center',
                    gap: '4px',
                    flexWrap: 'nowrap',
                    overflowX: 'hidden',
                  }}
                >
                  <button onClick={() => setCurrentPage(0)} style={buttonStyle}>
                    ⏮
                  </button>
                  <button onClick={goToPreviousPage} style={buttonStyle}>
                    ◀ Previous
                  </button>
                  <button onClick={goToNextPage} style={buttonStyle}>
                    Next ▶
                  </button>
                  <button
                    onClick={() =>
                      setCurrentPage(Math.ceil(clientList.length / pageSize) - 1)
                    }
                    style={buttonStyle}
                  >
                    ⏭
                  </button>
                </div>
              ) : (
                <button onClick={resetClientList} style={buttonStyle}>
                  🔁 Reset
                </button>
              )}
            </div>
          </CCard>
        </CCol>
      </CRow>
    </div>
  );
};

export default NavigationPanel;
