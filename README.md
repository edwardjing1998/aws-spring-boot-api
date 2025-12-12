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
  sysPrinTotal?: number;     // ⬅️ Best Practice: Total count from backend
  clientTotal?: number;      // ⬅️ Best Practice: Total count from backend
  reportOptionTotal?: number; // ⬅️ NEW: Report Option Total count from backend
  clientEmailTotal?: number; // ⬅️ NEW: Email Total count from backend
  clientPrefixTotal?: number; // ⬅️ NEW: Prefix Total count from backend
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

// ---- Sub-component for SysPrin Pager ----
// This ensures each row has its own isolated state for inputs
interface SysPrinPagerProps {
  clientId: string;
  currentPage: number;
  totalPages: number;
  isGroupExpanded: boolean;
  onPageChange: (targetPage: number) => void;
}

const SysPrinPager: React.FC<SysPrinPagerProps> = ({
  clientId,
  currentPage,
  totalPages,
  isGroupExpanded,
  onPageChange,
}) => {
  // Local state for the input field, isolated per row instance
  const [pageInputValue, setPageInputValue] = useState<string>((currentPage + 1).toString());

  // Track previous state of isGroup to handle the specific condition
  const prevIsGroupRef = useRef<boolean>(isGroupExpanded);

  // Sync input when currentPage prop changes
  useEffect(() => {
    const prevIsGroup = prevIsGroupRef.current;
    prevIsGroupRef.current = isGroupExpanded;

    // Only skip update if transitioning from true -> false
    if (prevIsGroup === true && isGroupExpanded === false) {
      return;
    }

    setPageInputValue((currentPage + 1).toString());
  }, [currentPage, isGroupExpanded]);

  const handleFirst = (e: React.MouseEvent) => {
    e.stopPropagation();
    if (currentPage > 0) onPageChange(0);
  };

  const handlePrev = (e: React.MouseEvent) => {
    e.stopPropagation();
    const val = parseInt(pageInputValue);
    if (!isNaN(val)) {
        const t = Math.max(0, val - 2);
        if(t !== currentPage) onPageChange(t);
    } else {
        // Fallback
        if (currentPage > 0) onPageChange(currentPage - 1);
    }
  };

  const handleNext = (e: React.MouseEvent) => {
    e.stopPropagation();
    const val = parseInt(pageInputValue);
    if (!isNaN(val)) {
        const t = val; // input "1" -> next is 1
        if(t < totalPages) onPageChange(t);
    } else {
         if (currentPage < totalPages - 1) onPageChange(currentPage + 1);
    }
  };

  const handleLast = (e: React.MouseEvent) => {
    e.stopPropagation();
    if (currentPage < totalPages - 1) onPageChange(totalPages - 1);
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
      <button
        type="button"
        onClick={handleFirst}
        style={{
          border: '0px',
          borderRadius: '4px',
          padding: '0 2px',
          fontSize: '1rem',
          lineHeight: '1.1',
          background: '#fff',
          cursor: 'pointer',
          height: '28px',
          color: 'blue',
          position: 'relative',
          top: '-2px',
        }}
        title="First Page"
      >
        ⏮
      </button>

      <button
        type="button"
        onClick={handlePrev}
        style={{
          border: '0px',
          borderRadius: '2px',
          padding: '0 2px',
          fontSize: '0.75rem',
          lineHeight: '1.1',
          background: '#fff',
          cursor: 'pointer',
          height: '28px',
          color: 'blue',
        }}
      >
        ◀ Prev
      </button>

      <div style={{ display: 'flex', alignItems: 'center', fontSize: '0.75rem', color: '#666' }}>
        <input
          type="text"
          value={pageInputValue}
          onChange={(e) => setPageInputValue(e.target.value)}
          onClick={(e) => e.stopPropagation()}
          style={{
            width: '30px',
            height: '28px',
            padding: 0,
            textAlign: 'center',
            margin: '0 4px',
            border: '1px solid #ccc',
            borderRadius: '3px',
            fontSize: '0.75rem',
          }}
        />
        of 
        <input
          type="text"
          value={totalPages}
          readOnly
          style={{
            width: '30px',
            height: '28px',
            padding: 0,
            textAlign: 'center',
            margin: '0 4px',
            border: '1px solid #ccc',
            borderRadius: '3px',
            fontSize: '0.75rem',
            backgroundColor: '#f0f0f0',
          }}
        />
      </div>

      <button
        type="button"
        onClick={handleNext}
        style={{
          border: '0px',
          borderRadius: '4px',
          padding: '0 2px',
          fontSize: '0.75rem',
          lineHeight: '1.1',
          background: '#fff',
          cursor: 'pointer',
          height: '28px',
          color: 'blue',
        }}
      >
        Next ▶
      </button>

      <button
        type="button"
        onClick={handleLast}
        style={{
          border: '0px',
          borderRadius: '4px',
          padding: '0 2px',
          fontSize: '1rem',
          lineHeight: '1.1',
          background: '#fff',
          cursor: 'pointer',
          height: '28px',
          color: 'blue',
          position: 'relative',
          top: '-2px',
        }}
        title="Last Page"
      >
        ⏭
      </button>
    </div>
  );
};


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

  const [clientTotal, setClientTotal] = useState<Record<string, number>>({});
  
  // NEW: State for total client count for main pagination
  const [totalClientsCount, setTotalClientsCount] = useState<number>(0);

  // NEW: Track total report options per client
  const [reportOptionTotalByClient, setReportOptionTotalByClient] = useState<Record<string, number>>({});

  // NEW: Track total report options per client
  const [clientEmailTotalByClient, setClientEmailTotalByClient] = useState<Record<string, number>>({});

  // NEW: Track total report options per client
  const [clientPrefixTotalByClient, setClientPrefixTotalByClient] = useState<Record<string, number>>({});

  // Ref Pattern: Keep refs in sync with state to avoid Stale Closures in AG Grid renderers
  const sysPrinPageRef = useRef(sysPrinPageByClient);
  const sysPrinTotalRef = useRef(sysPrinTotalByClient);
  const clientTotalRef = useRef(clientTotal);

  // Update refs whenever state changes
  sysPrinPageRef.current = sysPrinPageByClient;
  sysPrinTotalRef.current = sysPrinTotalByClient;
  clientTotalRef.current = clientTotal;

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

  // Optimization: Initialize totals (sysPrin & reportOption) from the client list
  // This runs every time the main client list is fetched or updated.
  useEffect(() => {
    // 0. Update global client count for main pagination
    if (clientList.length > 0 && typeof clientList[0].clientTotal === 'number') {
      setTotalClientsCount(clientList[0].clientTotal);
    }

    // 1. Client Total (Map)
    setClientTotal((prev) => {
      const next = { ...prev };
      let hasNewData = false;
      clientList.forEach((client) => {
        if (typeof client.clientTotal === 'number' && next[client.client] !== client.clientTotal) {
          next[client.client] = client.clientTotal;
          hasNewData = true;
        }
      });
      return hasNewData ? next : prev;
    });

    // 2. SysPrin Totals
    setSysPrinTotalByClient((prev) => {
      const next = { ...prev };
      let hasNewData = false;
      clientList.forEach((client) => {
        if (typeof client.sysPrinTotal === 'number' && next[client.client] !== client.sysPrinTotal) {
          next[client.client] = client.sysPrinTotal;
          hasNewData = true;
        }
      });
      return hasNewData ? next : prev;
    });

    // 3. Report Option Totals
    setReportOptionTotalByClient((prev) => {
      const next = { ...prev };
      let hasNewData = false;
      clientList.forEach((client) => {
        if (typeof client.reportOptionTotal === 'number' && next[client.client] !== client.reportOptionTotal) {
          next[client.client] = client.reportOptionTotal;
          hasNewData = true;
        }
      });
      return hasNewData ? next : prev;
    });

    // 4. Email Totals
    setClientEmailTotalByClient((prev) => {
      const next = { ...prev };
      let hasNewData = false;
      clientList.forEach((client) => {
        if (typeof client.clientEmailTotal === 'number' && next[client.client] !== client.clientEmailTotal) {
          next[client.client] = client.clientEmailTotal;
          hasNewData = true;
        }
      });
      return hasNewData ? next : prev;
    });

    // 5. Prefix Totals
    setClientPrefixTotalByClient((prev) => {
      const next = { ...prev };
      let hasNewData = false;
      clientList.forEach((client) => {
        if (typeof client.clientPrefixTotal === 'number' && next[client.client] !== client.clientPrefixTotal) {
          next[client.client] = client.clientPrefixTotal;
          hasNewData = true;
        }
      });
      return hasNewData ? next : prev;
    });

  }, [clientList]);

  // Calculate total pages for the main client list
  const totalClientPages = Math.max(1, Math.ceil(totalClientsCount / pageSize));

  const flattenedData = useMemo(() => {
    // FlattenClientData is now expected to use client.sysPrins directly.
    const rows = FlattenClientData(
      clientList,
      selectedClient,
      expandedGroups,
      isWildcardMode,
      {}, // <--- Force slicing from index 0
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
    
    // Boundary check
    if (nextPage >= totalClientPages) return;

    if (isWildcardMode && typeof onFetchWildcardPage === 'function') {
      onFetchWildcardPage(nextPage);
    } else {
      try {
        const data = await fetchClientsByPage(nextPage, pageSize);
        setClientList([]);
        setClientList(data);
        setCurrentPage(nextPage);
        // Reset inner pagination when main page changes
        setSysPrinPageByClient({});
        // Clear totals so they refresh from new page data
        setClientTotal({});
        setSysPrinTotalByClient({}); 
        setReportOptionTotalByClient({}); 
        setClientEmailTotalByClient({});
        setClientPrefixTotalByClient({});
      } catch (error: any) {
        console.error('Error fetching clients:', error);
        alert(`Error fetching client details: ${error.message}`);
      }
    }
  };

  const goToPreviousPage = async () => {
    const previousPage = currentPage - 1;
    
    // Boundary check
    if (previousPage < 0) return;

    if (isWildcardMode && typeof onFetchWildcardPage === 'function') {
      onFetchWildcardPage(previousPage);
    } else {
      try {
        const data = await fetchClientsByPage(previousPage, pageSize);
        setClientList([]);
        setClientList(data);
        setCurrentPage(previousPage);
        // Reset inner pagination when main page changes
        setSysPrinPageByClient({});
        setClientTotal({});
        setSysPrinTotalByClient({});
        setReportOptionTotalByClient({});
        setClientEmailTotalByClient({});
        setClientPrefixTotalByClient({});
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
      // Reset inner pagination when resetting list
      setSysPrinPageByClient({});
      setClientTotal({});
      setSysPrinTotalByClient({});
      setReportOptionTotalByClient({});
      setClientEmailTotalByClient({});
      setClientPrefixTotalByClient({});
    } catch (error) {
      console.error('Reset fetch failed:', error);
    }
  };
  
  // Handler for jumping to the last page of clients
  const goToLastPage = async () => {
    const lastPageIndex = Math.max(0, totalClientPages - 1);
    
    // Boundary check: already on last page
    if (currentPage === lastPageIndex) return;

    if (isWildcardMode && typeof onFetchWildcardPage === 'function') {
      onFetchWildcardPage(lastPageIndex);
    } else {
      try {
        const data = await fetchClientsByPage(lastPageIndex, pageSize);
        setClientList([]);
        setClientList(data);
        setCurrentPage(lastPageIndex);
        
        // Reset inner pagination
        setSysPrinPageByClient({});
        setClientTotal({});
        setSysPrinTotalByClient({});
        setReportOptionTotalByClient({});
        setClientEmailTotalByClient({});
        setClientPrefixTotalByClient({});
      } catch (error: any) {
        console.error('Error fetching last page:', error);
        alert(`Error fetching client details: ${error.message}`);
      }
    }
  };
  
  // Handler for jumping to the first page of clients
  const goToFirstPage = async () => {
    // Boundary check
    if (currentPage === 0) return;

    if (isWildcardMode && typeof onFetchWildcardPage === 'function') {
      onFetchWildcardPage(0);
    } else {
      try {
        const data = await fetchClientsByPage(0, pageSize);
        setClientList([]);
        setClientList(data);
        setCurrentPage(0);
        
        // Reset inner pagination
        setSysPrinPageByClient({});
        setClientTotal({});
        setSysPrinTotalByClient({});
        setReportOptionTotalByClient({});
        setClientEmailTotalByClient({});
        setClientPrefixTotalByClient({});
      } catch (error: any) {
        console.error('Error fetching first page:', error);
        alert(`Error fetching client details: ${error.message}`);
      }
    }
  };

  // ---- Grid columns ----
  // Wrap columnDefs in useMemo so it updates when sysPrinPageByClient/sysPrinTotalByClient changes.
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
        // Get current page from state
        const displayPage = sysPrinPageByClient[clientId] ?? 0;

        // ✅ Get total elements from state (populated via useEffect from clientList)
        const totalClientElements = clientTotal[clientId] ?? 0;

   //      alert("totalClientElements " + totalClientElements);
        
        // ✅ Get total elements from state (populated via useEffect from clientList)
        const totalSysPrinElements = sysPrinTotalByClient[clientId] ?? 0;
        
        // ✅ Calculate total pages
        const displaySysPrinTotalPages = Math.max(1, Math.ceil(totalSysPrinElements / SYS_PRIN_PAGE_SIZE));

        const isGroupExpanded = expandedGroups[clientId] ?? false;

        // ---- Pager row: call paged REST API and update parent clientList ----
        if (row.isPagerRow) {
          
          const handlePageChange = async (targetPage: number) => {
             // Fetch data
             try {
                const resp = await fetchSysPrinsByClientPaged(
                   clientId,
                   targetPage,
                   SYS_PRIN_PAGE_SIZE
                );
                
                // Update client list data
                setClientList((prev) =>
                    prev.map((c) =>
                      c.client === clientId ? { ...c, sysPrins: resp.items } : c,
                    ),
                );

                // Update page index state
                setSysPrinPageByClient((prev) => ({
                    ...prev,
                    [clientId]: targetPage,
                }));

                // Update total if provided (optional since we likely have it already)
                if (typeof resp.total === 'number') {
                    setSysPrinTotalByClient((prev) => ({
                    ...prev,
                    [clientId]: resp.total
                    }));
                }
             } catch (err: any) {
                console.error('Pager Error', err);
                alert('Failed to fetch page');
             }
          };

          return (
            <SysPrinPager 
                clientId={clientId}
                currentPage={displayPage}
                totalPages={displaySysPrinTotalPages}
                isGroupExpanded={isGroupExpanded}
                onPageChange={handlePageChange}
            />
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
  ], [sysPrinPageByClient, sysPrinTotalByClient, expandedGroups]); // Add dependencies here

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
      // ⬇️ REMOVED: Do not reset page index on group toggle, allowing persistence
      /*
      setSysPrinPageByClient((prev) => ({
        ...prev,
        [clientId]: 0,
      }));
      */

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
                justifyContent: 'flex-start', // Changed to flex-start
                paddingLeft: '12px',          // Added left padding
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
                    justifyContent: 'flex-start', // <-- Changed to flex-start here as well
                    gap: '4px',
                    flexWrap: 'nowrap',
                    overflowX: 'hidden',
                  }}
                >
                  <button onClick={goToFirstPage} style={{ ...buttonStyle, ...(currentPage === 0 && { cursor: 'default', color: '#ccc' }) }} disabled={currentPage === 0}>
                    ⏮
                  </button>
                  <button onClick={goToPreviousPage} style={{ ...buttonStyle, ...(currentPage === 0 && { cursor: 'default', color: '#ccc' }) }} disabled={currentPage === 0}>
                    ◀ Previous
                  </button>
                  <button onClick={goToNextPage} style={{ ...buttonStyle, ...(currentPage >= totalClientPages - 1 && { cursor: 'default', color: '#ccc' }) }} disabled={currentPage >= totalClientPages - 1}>
                    Next ▶
                  </button>
                  <button
                    onClick={goToLastPage}
                    style={{ ...buttonStyle, ...(currentPage >= totalClientPages - 1 && { cursor: 'default', color: '#ccc' }) }}
                    disabled={currentPage >= totalClientPages - 1}
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
