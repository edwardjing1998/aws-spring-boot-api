import React, { useEffect, useRef, useState, useMemo } from 'react';
import { CCard, CCol, CRow } from '@coreui/react';
import { ModuleRegistry } from 'ag-grid-community';
import { ClientSideRowModelModule } from 'ag-grid-community';
import { AgGridReact } from 'ag-grid-react';
import '../../../scss/sys-prin-configuration/client-information.scss';
import { FlattenClientData } from './utils/FlattenClientData';
import { fetchClientsByPage, resetClientListService } from './utils/ClientIntegrationService';

ModuleRegistry.registerModules([ClientSideRowModelModule]);

const NavigationPanel = ({
  onRowClick,
  clientList,
  setClientList,
  currentPage,
  setCurrentPage,
  isWildcardMode,
  setIsWildcardMode,
  onFetchWildcardPage,
  onFetchGroupDetails, // ✅ New prop: Function to fetch selectedGroupRow
}) => {
  const [selectedClient, setSelectedClient] = useState('ALL');
  const [tableData, setTableData] = useState([]);
  const [pageSize] = useState(5);
  const [expandedGroups, setExpandedGroups] = useState({});
  const gridApiRef = useRef(null);

  // ⭐ NEW: per-client sysPrin page index + page size
  const [sysPrinPageByClient, setSysPrinPageByClient] = useState({});
  const SYS_PRIN_PAGE_SIZE = 10; // adjust if you want more/less sysPrins per page

  const buttonStyle = {
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
      const next = {};
      clientList.forEach((client) => {
        next[client.client] = prev[client.client] ?? false;
      });
      return next;
    });
  }, [clientList]);

  // If clientList changes, you may also want to reset sysPrin page
  useEffect(() => {
    setSysPrinPageByClient({});
  }, [clientList]);

  const flattenedData = useMemo(() => {
    const rows = FlattenClientData(
      clientList,
      selectedClient,
      expandedGroups,
      isWildcardMode,
      sysPrinPageByClient,   // ⭐ NEW
      SYS_PRIN_PAGE_SIZE     // ⭐ NEW
    );

    // ✅ hard de-dupe for child rows
    const seen = new Set();
    const uniq = [];
    for (const r of rows) {
      if (r?.isGroup) {
        const key = `g:${r.client}`;
        if (!seen.has(key)) { seen.add(key); uniq.push(r); }
        continue;
      }
      const key = `s:${r.client}:${r.sysPrin}`;
      if (!seen.has(key)) { seen.add(key); uniq.push(r); }
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
      } catch (error) {
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

  const columnDefs = [
    {
      field: 'groupLabel',
      headerName: 'Clients',
      colSpan: (params) => (params.data?.isGroup ? 2 : 1),
      // ⭐ UPDATED: toolbar with Next/Previous for sysPrin
      cellRenderer: (params) => {
        const row = params.data;
        if (!row?.isGroup) return '';

        const clientId = row.client;
        const currentSysPrinPage = sysPrinPageByClient[clientId] ?? 0;

        const handleSysPrinPrev = (e) => {
          e.stopPropagation(); // don't toggle expand/collapse
          setSysPrinPageByClient((prev) => ({
            ...prev,
            [clientId]: Math.max(0, (prev[clientId] ?? 0) - 1),
          }));
        };

        const handleSysPrinNext = (e) => {
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
              justifyContent: 'space-between',
              alignItems: 'center',
              width: '100%',
            }}
          >
            <span>{row.groupLabel}</span>

            {/* ⭐ sysPrin pager toolbar */}
            <div style={{ display: 'flex', alignItems: 'center', gap: '4px' }}>
              <button
                type="button"
                onClick={handleSysPrinPrev}
                style={{
                  border: '1px solid #ccc',
                  borderRadius: '4px',
                  padding: '2px 6px',
                  fontSize: '0.75rem',
                  background: '#fff',
                  cursor: 'pointer',
                }}
              >
                ◀ SysPrin
              </button>
              <span style={{ fontSize: '0.75rem', color: '#666' }}>
                Page {currentSysPrinPage + 1}
              </span>
              <button
                type="button"
                onClick={handleSysPrinNext}
                style={{
                  border: '1px solid #ccc',
                  borderRadius: '4px',
                  padding: '2px 6px',
                  fontSize: '0.75rem',
                  background: '#fff',
                  cursor: 'pointer',
                }}
              >
                SysPrin ▶
              </button>
            </div>
          </div>
        );
      },
      valueGetter: (params) =>
        params.data?.isGroup ? `${params.data.client} - ${params.data.name}` : '',
      flex: 0.5,
      minWidth: 80,
    },
    {
      field: 'sysPrin',
      headerName: 'Sys Prin',
      width: 200,
      minWidth: 200,
      flex: 2,
      cellRenderer: (params) => {
        if (params.data?.isGroup) return '';
        return (
          <span>
            <span role="img" aria-label="gear" style={{ marginRight: '6px' }}>
              ⚙️
            </span>
            {params.value}
          </span>
        );
      },
      valueGetter: (params) => (params.data?.isGroup ? '' : params.data.sysPrin),
    },
  ];

  const defaultColDef = {
    flex: 1,
    resizable: true,
    minWidth: 120,
    sortable: false,
    filter: false,
    floatingFilter: false,
  };

  const rowClassRules = {
    'client-group-row': (params) => params.data?.isGroup && params.data?.groupLevel === 1,
  };

  const handleRowClicked = (event) => {
    const row = event.data;
    const clientId = row.client;

    setTimeout(() => {
      if (row.isGroup && clientId) {
        setExpandedGroups((prev) => {
          const currentlyExpanded = prev[clientId] ?? false;
          const newState = {};
          clientList.forEach((c) => {
            newState[c.client] = false;
          });
          newState[clientId] = !currentlyExpanded;
          return newState;
        });

        // When switching group, reset its sysPrin page to 0
        setSysPrinPageByClient((prev) => ({
          ...prev,
          [clientId]: 0,
        }));

        // 🟢 Defer onRowClick and onFetchGroupDetails
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
                style={{ height: '100%', width: '100%', overflowY: 'auto', overflowX: 'hidden' }}
              >
                <AgGridReact
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
                  // ✅ NEW:
                  getRowId={(params) => {
                    const d = params.data;
                    if (d?.isGroup) return `g:${d.client}`;
                    return `s:${d.client}:${d.sysPrin}`;
                  }}
                  deltaRowDataMode={true}
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
                    onClick={() => setCurrentPage(Math.ceil(clientList.length / pageSize) - 1)}
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
