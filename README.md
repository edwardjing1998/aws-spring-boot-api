import React, { useEffect, useMemo, useState } from 'react';
import { Typography, IconButton, Button, Tooltip } from '@mui/material';
import { CRow, CCol } from '@coreui/react';
import VisibilityIcon from '@mui/icons-material/Visibility';
import EditIcon from '@mui/icons-material/Edit';
import DeleteIcon from '@mui/icons-material/Delete';

import AtmCashPrefixDetailWindow from './utils/AtmCashPrefixDetailWindow';

const PAGE_SIZE = 10;

// --- Interfaces ---

export interface AtmCashPrefixRow {
  billingSp: string;
  prefix: string;
  atmCashRule: string; // '0', '1', or sometimes '' from inputs
  [key: string]: any;
}

interface EditAtmCashPrefixProps {
  selectedGroupRow: {
    client?: string;
    clientId?: string;
    billingSp?: string;
    clientPrefixTotal?: number;
    sysPrinsPrefixes?: AtmCashPrefixRow[];
    [key: string]: any;
  } | null;
  onDataChange?: (updatedGroupRow: any) => void;
}

// --- Helpers ---

const equalPrefixes = (a: AtmCashPrefixRow[] = [], b: AtmCashPrefixRow[] = []) =>
  a.length === b.length &&
  a.every((x, i) => {
    const y = b[i] || {};
    return (
      String(x.billingSp ?? '') === String(y.billingSp ?? '') &&
      String(x.prefix ?? '') === String(y.prefix ?? '') &&
      String(x.atmCashRule ?? '') === String(y.atmCashRule ?? '')
    );
  });

const EditAtmCashPrefix: React.FC<EditAtmCashPrefixProps> = ({ selectedGroupRow = {}, onDataChange }) => {
  // prefixes now holds JUST the current page from API
  const [prefixes, setPrefixes] = useState<AtmCashPrefixRow[]>([]);
  const [selectedPrefix, setSelectedPrefix] = useState<string>('');
  const [page, setPage] = useState<number>(0);

  // NEW: Trigger for re-fetching data without changing page
  const [refreshKey, setRefreshKey] = useState<number>(0);

  // NEW: Track locally added/removed count to update pagination immediately
  const [localCountAdjustment, setLocalCountAdjustment] = useState<number>(0);

  // Determine Client ID (Billing SP usually preferred for prefixes, fallback to client)
  const clientId = selectedGroupRow?.billingSp || selectedGroupRow?.client || '';

  // Base total from props + local changes
  const baseTotalCount = selectedGroupRow?.clientPrefixTotal || 0;
  const totalCount = Math.max(0, baseTotalCount + localCountAdjustment);

  // Reset local adjustment when parent updates
  useEffect(() => {
    setLocalCountAdjustment(0);
  }, [baseTotalCount, clientId]);

  // Window state (handles detail/edit/delete/new)
  const [winOpen, setWinOpen] = useState<boolean>(false);
  const [winMode, setWinMode] = useState<'detail' | 'edit' | 'delete' | 'new'>('detail');
  const [winRow, setWinRow] = useState<AtmCashPrefixRow | null>(null);

  // Push up helper
  const pushUp = (nextList: AtmCashPrefixRow[]) => {
    if (typeof onDataChange !== 'function') return;
    // Note: With server-side paging, pushUp might send incomplete lists if we just send 'prefixes'.
    // Preserving logic structure, but be aware this sends only the current page to the parent.
    const existing = Array.isArray(selectedGroupRow?.sysPrinsPrefixes)
      ? selectedGroupRow?.sysPrinsPrefixes
      : [];
    if (equalPrefixes(existing, nextList)) return; // no-op, avoid loops
    onDataChange({ ...(selectedGroupRow ?? {}), sysPrinsPrefixes: nextList });
  };

  // API Fetch for Server-Side Pagination
  useEffect(() => {
    if (!clientId) return;

    const fetchData = async () => {
      try {
        const url = `http://localhost:8089/client-sysprin-reader/api/client/sysprin-prefix/${encodeURIComponent(clientId)}/pagination?page=${page}&size=${PAGE_SIZE}`;
        
        const resp = await fetch(url, {
            method: 'GET',
            headers: { accept: '*/*' },
        });

        if (resp.ok) {
            const json = await resp.json();
            // Assuming API returns array or standard Page content
            const nextRows: AtmCashPrefixRow[] = Array.isArray(json) ? json : (json.content || []);
            setPrefixes(nextRows);
        } else {
            console.error("Failed to fetch prefixes");
            setPrefixes([]);
        }
      } catch (error) {
        console.error("Error fetching prefixes:", error);
        setPrefixes([]);
      }
    };

    fetchData();
  }, [clientId, page, refreshKey]);

  const pageCount = Math.max(1, Math.ceil(totalCount / PAGE_SIZE));
  // Since prefixes IS the current page now, we just use it directly
  const pageData = prefixes;

  const cellStyle = (isSelected: boolean): React.CSSProperties => ({
    backgroundColor: isSelected ? '#cce5ff' : 'white',
    minHeight: '25px',
    display: 'flex',
    alignItems: 'center',
    justifyContent: 'flex-start',
    fontSize: '0.78rem',
    fontWeight: 400,
    padding: '0 10px',
    borderBottom: '1px dotted #ddd',
    cursor: 'pointer',
  });

  const headerStyle: React.CSSProperties = {
    ...cellStyle(false),
    fontWeight: 'bold',
    backgroundColor: '#f0f0f0',
    borderBottom: '1px dotted #ccc',
    cursor: 'default',
  };

  const atmCashLabel = (rule: string | number) => {
    if (rule === '0' || rule === 0) return 'Destroy';
    if (rule === '1' || rule === 1) return 'Return';
    return 'N/A';
  };

  // Row selection (just for highlighting)
  const handleRowClick = (item: AtmCashPrefixRow) => setSelectedPrefix(item.prefix);

  // Open windows
  const openDetail = (item: AtmCashPrefixRow, e?: React.MouseEvent) => {
    e?.stopPropagation();
    setWinRow(item);
    setWinMode('detail');
    setWinOpen(true);
  };
  const openEdit = (item: AtmCashPrefixRow, e?: React.MouseEvent) => {
    e?.stopPropagation();
    setWinRow(item);
    setWinMode('edit');
    setWinOpen(true);
  };
  const openDelete = (item: AtmCashPrefixRow, e?: React.MouseEvent) => {
    e?.stopPropagation();
    setWinRow(item);
    setWinMode('delete');
    setWinOpen(true);
  };
  const openNew = () => {
    setWinRow({ billingSp: selectedGroupRow?.billingSp || '', prefix: '', atmCashRule: '' });
    setWinMode('new');
    setWinOpen(true);
  };

  const isPrevDisabled = page <= 0;
  // Enable next if we have a full page (indicating potentially more data) OR if calculation says so
  const isNextDisabled = totalCount !== undefined 
    ? (page >= pageCount - 1 && prefixes.length < PAGE_SIZE)
    : (prefixes.length < PAGE_SIZE);

  return (
    <>
      <CRow className="mb-3">
        <CCol xs={12}>
          <div style={{ height: '400px', marginBottom: '4px', marginTop: '5px' }}>
            <div className="d-flex align-items-center" style={{ padding: '0.25rem 0.5rem', height: '100%' }}>
              <div style={{ width: '100%', height: '100%' }}>
                <div style={{ display: 'flex', flexDirection: 'column', height: '100%' }}>
                  <div
                    style={{
                      flex: 1,
                      display: 'grid',
                      gridTemplateColumns: '1fr 1fr 1fr 1fr',
                      rowGap: '0px',
                      columnGap: '4px',
                      minHeight: '250px',
                      alignContent: 'start',
                    }}
                  >
                    <div style={headerStyle}>Billing SP</div>
                    <div style={headerStyle}>Prefix</div>
                    <div style={headerStyle}>ATM/Cash</div>
                    <div style={headerStyle}>Action</div>

                    {pageData.map((item, index) => {
                      const isSelected = item.prefix === selectedPrefix;
                      return (
                        <React.Fragment key={`${item.billingSp}-${item.prefix}-${index}`}>
                          <div style={cellStyle(isSelected)} onClick={() => handleRowClick(item)}>
                            {item.billingSp}
                          </div>
                          <div style={cellStyle(isSelected)} onClick={() => handleRowClick(item)}>
                            {item.prefix}
                          </div>
                          <div style={cellStyle(isSelected)} onClick={() => handleRowClick(item)}>
                            {atmCashLabel(item.atmCashRule)}
                          </div>

                          {/* Action column */}
                          <div style={cellStyle(false)} onClick={(e) => e.stopPropagation()}>
                            <Tooltip title="Detail">
                              <IconButton size="small" aria-label="detail" onClick={(e) => openDetail(item, e)}>
                                <VisibilityIcon fontSize="small" />
                              </IconButton>
                            </Tooltip>
                            <Tooltip title="Edit">
                              <IconButton size="small" aria-label="edit" onClick={(e) => openEdit(item, e)}>
                                <EditIcon fontSize="small" />
                              </IconButton>
                            </Tooltip>
                            <Tooltip title="Delete">
                              <IconButton size="small" aria-label="delete" onClick={(e) => openDelete(item, e)}>
                                <DeleteIcon fontSize="small" />
                              </IconButton>
                            </Tooltip>
                          </div>
                        </React.Fragment>
                      );
                    })}
                  </div>

                  {/* Pager */}
                  <div
                    style={{
                      marginTop: '0px',
                      display: 'flex',
                      justifyContent: 'space-between',
                      alignItems: 'center',
                    }}
                  >
                    <Button
                      variant="text"
                      size="small"
                      sx={{ fontSize: '0.7rem', padding: '2px 8px', minWidth: 'unset', textTransform: 'none' }}
                      onClick={() => setPage((p) => Math.max(p - 1, 0))}
                      disabled={isPrevDisabled}
                    >
                      ◀ Previous
                    </Button>
                    <Typography fontSize="0.75rem">
                      Page {prefixes.length > 0 ? page + 1 : 0} of {prefixes.length > 0 ? pageCount : 0}
                    </Typography>
                    <Button
                      variant="text"
                      size="small"
                      sx={{ fontSize: '0.7rem', padding: '2px 8px', minWidth: 'unset', textTransform: 'none' }}
                      onClick={() => setPage((p) => Math.min(p + 1, Math.max(pageCount - 1, 0)))}
                      disabled={isNextDisabled}
                    >
                      Next ▶
                    </Button>
                  </div>

                  {/* New button centered under pager */}
                  <div
                    style={{
                      marginTop: '8px',
                      display: 'flex',
                      justifyContent: 'center',
                      alignItems: 'center',
                    }}
                  >
                    <Button
                      onClick={openNew}
                      variant="contained"
                      color="primary"
                      size="small"
                      sx={{ textTransform: 'none', fontSize: '0.8rem', px: 2.5, py: 0.75 }}
                    >
                      New
                    </Button>
                  </div>

                </div>
              </div>
            </div>
          </div>
        </CCol>
      </CRow>

      {/* Unified Window */}
      <AtmCashPrefixDetailWindow
        open={winOpen}
        mode={winMode}
        row={winRow}
        onClose={() => setWinOpen(false)}
        onConfirm={async (_mode: string, draft: AtmCashPrefixRow) => {
          // EDIT result
          setRefreshKey(prev => prev + 1);
          setSelectedPrefix(draft.prefix);
        }}
        onCreated={(created: AtmCashPrefixRow) => {
          // ✅ AUTO PAGING LOGIC
          setLocalCountAdjustment(prev => prev + 1);

          // Calculate new last page index
          const newTotalCount = totalCount + 1;
          const newLastPage = Math.max(0, Math.ceil(newTotalCount / PAGE_SIZE) - 1);

          if (page === newLastPage) {
             if (prefixes.length < PAGE_SIZE) {
                // Append locally if space exists on current (last) page
                const normalized = { ...created, atmCashRule: String(created.atmCashRule ?? '') };
                setPrefixes(prev => [...prev, normalized]);
             } else {
                // Page full, move to next
                setPage(page + 1);
                const normalized = { ...created, atmCashRule: String(created.atmCashRule ?? '') };
                setPrefixes([normalized]); // Optimistic
             }
          } else {
             // Not on last page, do nothing to view
          }
          
          setSelectedPrefix(created.prefix);
        }}
        onDeleted={(_deleted: AtmCashPrefixRow) => {
          setLocalCountAdjustment(prev => prev - 1);
          setRefreshKey(prev => prev + 1);
        }}
      />
    </>
  );
};

export default EditAtmCashPrefix;
