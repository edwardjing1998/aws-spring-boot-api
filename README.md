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
import { fetchClientsByPage, resetClientListService } from './utils/ClientIntegrationService';

ModuleRegistry.registerModules([ClientSideRowModelModule]);

// ---- Types ----

export interface ClientRow {
  client: string;
  name: string;
  // anything else coming from the backend
  [key: string]: any;
}

export interface NavigationRow {
  client: string;
  name?: string;
  sysPrin?: string;
  isGroup?: boolean;
  groupLevel?: number;
  groupLabel?: string;
  isFirstSysPrinRow?: boolean;
  // other fields from FlattenClientData
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
  onFetchGroupDetails, // ✅ Function to fetch selectedGroupRow
}) => {
  const [selectedClient, setSelectedClient] = useState<string>('ALL');
  const [tableData, setTableData] = useState<NavigationRow[]>([]);
  const [pageSize] = useState<number>(5);
  const [expandedGroups, setExpandedGroups] = useState<Record<string, boolean>>({});
  const gridApiRef = useRef<GridApi<NavigationRow> | null>(null);

  // ⭐ per-client sysPrin page index + page size
  const [sysPrinPageByClient, setSysPrinPageByClient] = useState<Record<string, number>>({});
  const SYS_PRIN_PAGE_SIZE = 10;

  const buttonStyle: React.CSSProperties = {
    border: 'none',
    background: 'none',
    padding: '6px 12px',
    cursor: 'pointer',
    fontSize: '0.85rem',
    color: '#555',
    whiteSpace: 'nowrap',
  };

  // Initialize expandedGroups keys whenever clientList changes
  useEffect(() => {
    setExpandedGroups((prev) => {
      const next: Record<string, boolean> = {};
      clientList.forEach((client) => {
        next[client.client] = prev[client.client] ?? false;
      });
      return next;
    });
  }, [clientList]);

  // Reset sysPrin pages when clientList changes
  useEffect(() => {
    setSysPrinPageByClient({});
  }, [clientList]);

  const flattenedData = useMemo(() => {
    const rows = FlattenClientData(
      clientList,
      selectedClient,
      expandedGroups,
      isWildcardMode,
      sysPrinPageByClient, // ⭐ NEW
      SYS_PRIN_PAGE_SIZE,  // ⭐ NEW
    ) as NavigationRow[];

    // ✅ hard de-dupe for child rows AND mark first sysPrin row per client
    const seen = new Set<string>();
    const firstChildSeenByClient: Record<string, boolean> = {};
    const uniq: NavigationRow[] = [];

    for (const r of rows) {
      if (r?.isGroup) {
        const key = `g:${r.client}`;
        if (!seen.has(key)) {
          seen.add(key);
          uniq.push(r);
        }
        continue;
      }

      const key = `s:${r.client}:${r.sysPrin}`;
      if (seen.has(key)) continue;

      seen.add(key);

      // mark first sysPrin row for this client
      if (!firstChildSeenByClient[r.client]) {
        firstChildSeenByClient[r.client] = true;
        r.isFirstSysPrinRow = true; // ⭐ mark
      }

      uniq.push(r);
    }

    return uniq;
  }, [clientList, selectedClient, expandedGroups, isWildcardMode, sysPrinPageByClient]);

  useEffect(() => {
    setTableData(flattenedData);
  }, [flattenedData]);

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

  const columnDefs: ColDef<NavigationRow>[] = [
    {
      field: 'groupLabel',
      headerName: 'Clients',
      colSpan: (params) => (params.data?.isGroup ? 2 : 1),
      // 👉 now ONLY render label, no SysPrin buttons here
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

        // ⭐ group row: still blank in SysPrin column
        if (row.isGroup) return '';

        const clientId = row.client;
        const currentSysPrinPage = sysPrinPageByClient[clientId] ?? 0;

        // 👉 FIRST sysPrin row for this client: render toolbar instead of value
        if (row.isFirstSysPrinRow) {
          const handleSysPrinPrev = (e: React.MouseEvent<HTMLButtonElement>) => {
            e.stopPropagation(); // prevent row click
            setSysPrinPageByClient((prev) => ({
              ...prev,
              [clientId]: Math.max(0, (prev[clientId] ?? 0) - 1),
            }));
          };

          const handleSysPrinNext = (e: React.MouseEvent<HTMLButtonElement>) => {
            e.stopPropagation();
            setSysPrinPageByClient((prev) => ({
              ...prev,
              [clientId]: (prev[clientId] ?? 0) + 1,
            }));
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
                  border: '1px dotted blue',
                  borderRadius: '4px',
                  padding: '0 4px',
                  fontSize: '0.75rem',
                  lineHeight: '1.1',
                  background: '#fff',
                  cursor: 'pointer',
                  height: '26px',
                  color: 'blue'
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
                  border: '1px dotted blue',
                  borderRadius: '4px',
                  padding: '0 4px',
                  fontSize: '0.75rem',
                  lineHeight: '1.1',
                  background: '#fff',
                  cursor: 'pointer',
                  height: '25px',
                  color: 'blue'
                }}
              >
                Next ▶
              </button>
            </div>
          );
        }

        // 👉 other sysPrin rows: show the sysPrin value with gear icon
        return (
          <span>
            <span role="img" aria-label="gear" style={{ marginRight: '6px' }}>
              ⚙️
            </span>
            {params.value}
          </span>
        );
      },
      valueGetter: (params) => (params.data?.isGroup ? '' : params.data?.sysPrin ?? ''),
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

    setTimeout(() => {
      if (row.isGroup && clientId) {
        setExpandedGroups((prev) => {
          const currentlyExpanded = prev[clientId] ?? false;
          const newState: Record<string, boolean> = {};
          clientList.forEach((c) => {
            newState[c.client] = false;
          });
          newState[clientId] = !currentlyExpanded;
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
                        return `s:${d.client}:${d.sysPrin}`;
                    }}
                    />
              </div>
            </div>

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
