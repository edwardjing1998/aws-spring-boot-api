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
  // ⬇️ NEW
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
  onClearSelectedData,   // ⬅️ new
}) => {
  const [selectedClient, setSelectedClient] = useState<string>('ALL');
  const [tableData, setTableData] = useState<NavigationRow[]>([]);
  const [pageSize] = useState<number>(5);
  const [expandedGroups, setExpandedGroups] = useState<Record<string, boolean>>({});
  const gridApiRef = useRef<GridApi<NavigationRow> | null>(null);

  // per-client SysPrin page index from backend (0-based)
  const [sysPrinPageByClient, setSysPrinPageByClient] = useState<Record<string, number>>({});
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

  const goToPreviousPage = () => {
    const previousPage = Math.max(0, currentPage - 1);
    if (isWildcardMode && typeof onFetchWildcardPage === 'function') {
      onFetchWildcardPage(previousPage);
    } else {
      setCurrentPage(previousPage);
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
  const columnDefs: ColDef<NavigationRow>[] = [
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
        const currentSysPrinPage = sysPrinPageByClient[clientId] ?? 0;

        // ---- Pager row: call paged REST API and update parent clientList ----
        if (row.isPagerRow) {
          const handleSysPrinPrev = async (
            e: React.MouseEvent<HTMLButtonElement>,
          ) => {
            e.stopPropagation();

            const current = sysPrinPageByClient[clientId] ?? 0;
            const targetPage = Math.max(0, current - 1);

            try {
              const resp = await fetchSysPrinsByClientPaged(
                clientId,
                targetPage,
                SYS_PRIN_PAGE_SIZE,
              );

              if (resp.items.length === 0 && resp.total > 0 && targetPage > 0) {
                // Already at the first page (or backend refused); do not move.
                return;
              }

              // Replace sysPrins array for this client in parent state
              setClientList((prev) =>
                prev.map((c) =>
                  c.client === clientId ? { ...c, sysPrins: resp.items } : c,
                ),
              );

              // Use page index returned by backend
              setSysPrinPageByClient((prev) => ({
                ...prev,
                [clientId]: resp.page,
              }));
            } catch (err: any) {
              console.error('Error fetching previous SysPrins', err);
              alert(
                `Error fetching SysPrins for client ${clientId}: ${err.message}`,
              );
            }
          };

          const handleSysPrinNext = async (
            e: React.MouseEvent<HTMLButtonElement>,
          ) => {
            e.stopPropagation();

            const current = sysPrinPageByClient[clientId] ?? 0;
            const targetPage = current + 1;

            try {
              const resp = await fetchSysPrinsByClientPaged(
                clientId,
                targetPage,
                SYS_PRIN_PAGE_SIZE,
              );

              // If backend says there are total items, compute max page:
              const totalPages =
                resp.size > 0
                  ? Math.max(1, Math.ceil(resp.total / resp.size))
                  : 1;

              if (resp.items.length === 0 && totalPages > 0 && targetPage >= totalPages) {
                // Past last page; you could show a toast if you like.
                return;
              }

              setClientList((prev) =>
                prev.map((c) =>
                  c.client === clientId ? { ...c, sysPrins: resp.items } : c,
                ),
              );

              setSysPrinPageByClient((prev) => ({
                ...prev,
                [clientId]: resp.page,
              }));
            } catch (err: any) {
              console.error('Error fetching next SysPrins', err);
              alert(
                `Error fetching SysPrins for client ${clientId}: ${err.message}`,
              );
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
              <button
                type="button"
                onClick={handleSysPrinPrev}
                style={{
                  border: '0px dotted blue',
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
                ◀ Previous
              </button>
              <span style={{ fontSize: '0.75rem', color: '#666' }}>
                Page {currentSysPrinPage + 1}
              </span>
              <button
                type="button"
                onClick={handleSysPrinNext}
                style={{
                  border: '0px dotted blue',
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
  ];

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




export interface SysPrinDTO {
  client: string;
  sysPrin: string;
  // plus all other fields from the backend
  [key: string]: any;
}

/**
 * Shape of the data consumed by the UI.
 * We map the backend response to this structure.
 */
export interface SysPrinPageResponse {
  items: SysPrinDTO[];   // list of SysPrins for this page
  page: number;          // current page index (0-based)
  size: number;          // page size
  total: number;         // total number of SysPrins for this client
}

/**
 * Internal interface representing the raw JSON structure from your backend.
 * Based on the sample: { content: [], page: 0, size: 10, totalElements: 18, totalPages: 2 }
 */
interface BackendSysPrinResponse {
  content: SysPrinDTO[];
  page: number;
  size: number;
  totalElements: number;
  totalPages: number;
}

/**
 * Fetch paged SysPrins for a client.
 *
 * GET /sysprins/client/{clientId}/paged?page={page}&size={size}
 */
export async function fetchSysPrinsByClientPaged(
  clientId: string,
  page: number,
  size: number,
): Promise<SysPrinPageResponse> {
  const url = `http://localhost:8089/client-sysprin-reader/api/sysprins/client/${encodeURIComponent(
    clientId,
  )}/paged?page=${encodeURIComponent(page)}&size=${encodeURIComponent(size)}`;

  const resp = await fetch(url, {
    method: 'GET',
    headers: { accept: '*/*' },
  });

  if (!resp.ok) {
    const text = await resp.text().catch(() => '');
    throw new Error(
      `Failed to fetch SysPrins for client ${clientId}, page=${page}, size=${size}: ${resp.status} ${text}`,
    );
  }

  const json = await resp.json();

  // 1. Check for the standard Spring Data / Paged structure provided in your example
  if (json && Array.isArray(json.content)) {
    const typed = json as BackendSysPrinResponse;
    return {
      items: typed.content,
      page: typed.page,
      size: typed.size,
      total: typed.totalElements,
    };
  }

  // 2. Fallback: if backend returns plain array
  if (Array.isArray(json)) {
    const arr = json as SysPrinDTO[];
    return {
      items: arr,
      page,
      size,
      total: arr.length,
    };
  }

  // 3. Fallback: Generic mapping if structure is different but has items/total
  return {
    items: json.items ?? [],
    page: typeof json.page === 'number' ? json.page : page,
    size: typeof json.size === 'number' ? json.size : size,
    total: typeof json.total === 'number' ? json.total : (json.items?.length ?? 0),
  };
}






