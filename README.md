import React, { useEffect, useMemo, useState } from 'react';
import { Typography, Button, IconButton, Tooltip } from '@mui/material';
import InfoOutlinedIcon from '@mui/icons-material/InfoOutlined';
import EditOutlinedIcon from '@mui/icons-material/EditOutlined';
import DeleteOutlineIcon from '@mui/icons-material/DeleteOutline';
import { CCard, CCardBody } from '@coreui/react';

import ClientReportWindow from '../utils/ClientReportWindow';

import { 
  ClientReportRow, 
  ClientReportItem, 
  EditClientReportProps 
} from './EditClientReport.types';

import { 
  editRowCellStyle, 
  editHeaderCellStyle
} from './EditClientReport.styles';

const PAGE_SIZE = 8;

const EditClientReport: React.FC<EditClientReportProps> = ({ selectedGroupRow, isEditable, onDataChange }) => {
  // reports now holds JUST the current page from API
  const [reports, setReports] = useState<ClientReportRow[]>([]);
  const [page, setPage] = useState<number>(0);
  
  // NEW: Trigger for re-fetching data without changing page
  const [refreshKey, setRefreshKey] = useState<number>(0);

  // NEW: Track locally added/removed count to update pagination immediately
  // This bridges the gap until the parent refreshes 'selectedGroupRow'
  const [localCountAdjustment, setLocalCountAdjustment] = useState<number>(0);

  // Derive clientId and total count from props
  const clientId =
    selectedGroupRow?.client ||
    selectedGroupRow?.clientId ||
    selectedGroupRow?.billingSp ||
    '';
    
  // Base total from props + local changes
  const baseTotalCount = selectedGroupRow?.reportOptionTotal || 0;
  // Ensure totalCount never goes below 0
  const totalCount = Math.max(0, baseTotalCount + localCountAdjustment);

  // Reset local adjustment when parent updates (assumed refresh)
  useEffect(() => {
    setLocalCountAdjustment(0);
  }, [baseTotalCount, clientId]);

  // modal state
  const [modalOpen, setModalOpen] = useState<boolean>(false);
  const [modalMode, setModalMode] = useState<'detail' | 'edit' | 'new' | 'delete'>('detail'); 
  const [modalRowIdx, setModalRowIdx] = useState<number | null>(null); // null => creating new
  const [draftRow, setDraftRow] = useState<ClientReportRow | null>(null); // snapshot after delete or "new"

  // API Fetch for Server-Side Pagination
  useEffect(() => {
    if (!clientId) return;

    const fetchData = async () => {
      try {
        const url = `http://localhost:8089/client-sysprin-reader/api/client/report-option/${encodeURIComponent(clientId)}?page=${page}&size=${PAGE_SIZE}`;
        
        const resp = await fetch(url, {
            method: 'GET',
            headers: { accept: '*/*' },
        });

        if (resp.ok) {
            const options: ClientReportItem[] = await resp.json();
            
            // Normalize incoming data to table row format
            const nextRows: ClientReportRow[] = Array.isArray(options) ? options.map((option) => ({
                reportName: option?.reportDetails?.queryName || '',
                reportId: option?.reportDetails?.reportId ?? option?.reportId ?? null,
                receive: option?.receiveFlag ? '1' : '2',
                destination: option?.outputTypeCd !== undefined ? String(option.outputTypeCd) : '0',
                fileText: option?.fileTypeCd !== undefined ? String(option.fileTypeCd) : '0',
                email: option?.emailFlag === 1 ? '1' : option?.emailFlag === 2 ? '2' : '0',
                password: option?.reportPasswordTx || '',
                emailBodyTx: option?.emailBodyTx || '',
                fileExt: option?.fileExt ?? option?.reportDetails?.fileExt ?? '',
            })) : [];

            setReports(nextRows);
        } else {
            console.error("Failed to fetch client reports");
            setReports([]);
        }
      } catch (error) {
        console.error("Error fetching client reports:", error);
        setReports([]);
      }
    };

    fetchData();
  }, [clientId, page, refreshKey]); // ✅ Added refreshKey to dependencies

  // pagination metrics
  const pageCount = Math.max(1, Math.ceil(totalCount / PAGE_SIZE));
  // Since reports IS the current page now, we just use it directly
  const pageData = reports;

  const labelCell = (text: string, align: 'left' | 'center' | 'right' = 'center') => (
    <div style={{ ...editRowCellStyle, justifyContent: align }}>
      <Typography noWrap sx={{ fontSize: '0.72rem', lineHeight: 1.1 }}>
        {text}
      </Typography>
    </div>
  );

  // label mappers
  const mapReceive = (v: string) => (v === '1' ? 'Yes' : v === '2' ? 'No' : 'None');
  const mapDestination = (v: string) => (v === '1' ? 'File' : v === '2' ? 'Print' : 'None');

  // actions -> detail/edit/delete
  const openDetail = (rowIdx: number) => {
    setModalMode('detail');
    setModalRowIdx(rowIdx);
    setDraftRow(null);
    setModalOpen(true);
  };
  const openEdit = (rowIdx: number) => {
    setModalMode('edit');
    setModalRowIdx(rowIdx);
    setDraftRow(null);
    setModalOpen(true);
  };
  const openDelete = (rowIdx: number) => {
    setModalMode('delete');
    setModalRowIdx(rowIdx);
    setDraftRow(null);
    setModalOpen(true);
  };

  // actual deletion logic — KEEP DIALOG OPEN
  const handleRemove = (rowIdx: number) => {
    // For server-side deletion, you would ideally call API here.
    // For now, triggering a refresh to sync.
    setLocalCountAdjustment(prev => prev - 1);
    setRefreshKey(prev => prev + 1);
  };

  // "New" button
  const handleNewClick = () => {
    if (!isEditable) return;
    setModalMode('new');
    setModalRowIdx(null);
    setDraftRow({
      reportName: '',
      reportId: null,
      receive: '0',
      destination: '0',
      fileText: '0',
      email: '0',
      password: '',
      emailBodyTx: '',
      fileExt: '',          // ⬅️ start empty
    });
    setModalOpen(true);
  };

  // save/create from modal
  const handleSaveFromModal = (updatedRow: Partial<ClientReportRow>) => {
    if (modalRowIdx == null) {
      // creating a new row
      const normalized: ClientReportRow = {
        reportName: updatedRow?.reportName ?? '',
        reportId: updatedRow?.reportId ?? null,
        receive: updatedRow?.receive != null ? String(updatedRow.receive) : '0',
        destination: updatedRow?.destination != null ? String(updatedRow.destination) : '0',
        fileText: updatedRow?.fileText != null ? String(updatedRow.fileText) : '0',
        email: updatedRow?.email != null ? String(updatedRow.email) : '0',
        password: updatedRow?.password ?? '',
        emailBodyTx: updatedRow?.emailBodyTx ?? '',
        fileExt: updatedRow?.fileExt ?? '',   // ⬅️ take the string directly
      };
      
      // ❌ REMOVED: Do NOT update parent (PreviewClientReports) automatically
      // pushUp([...reports, normalized]); 

      // ✅ AUTO PAGING LOGIC
      // 1. Calculate if we are currently on the last page (before adding)
      // totalCount here includes the 'localCountAdjustment' added previously, 
      // but we haven't incremented it yet for THIS item.
      
      // Wait, we need to compare current page to the last page index of the EXISTING list.
      const currentLastPageIdx = Math.max(0, Math.ceil(totalCount / PAGE_SIZE) - 1);
      const isLastPage = page === currentLastPageIdx;

      // 2. Increment local count
      setLocalCountAdjustment(prev => prev + 1);

      if (isLastPage) {
        if (reports.length < PAGE_SIZE) {
            // Case 1: Page has space. Append directly to view.
            setReports(prev => [...prev, normalized]);
        } else {
            // Case 2: Page full. Move to next page.
            setPage(page + 1);
            setReports([normalized]); // Optimistic next page state
        }
      } else {
        // Case 3: Not on last page. Do nothing to view.
        // User stays on current page.
      }

      // keep dialog open
    } else {
      // updating existing row
      // ... existing edit logic passed to parent ...
      // For server side, we just refresh to see updates if parent handled it
      setRefreshKey(prev => prev + 1);
    }
  };

  // delete confirm from modal (called after successful DELETE)
  const handleDeleteFromModal = () => {
    if (modalRowIdx == null) return;
    const snapshot = reports[modalRowIdx] || null;
    setDraftRow(snapshot);
    setModalRowIdx(null);
    handleRemove(modalRowIdx);
  };

  const currentRow = modalRowIdx == null ? draftRow : reports[modalRowIdx] || null;

  return (
    <div style={{ padding: '10px' }}>
      <CCard>
        <CCardBody>
          {/* table */}
          <div style={{ height: '280px', marginBottom: '4px', marginTop: '2px' }}>
            <div
              style={{
                display: 'grid',
                gridTemplateColumns: '4fr 1fr 1fr 3fr',
                columnGap: '4px',
              }}
            >
              {/* headers */}
              {['Report Name', 'Receive', 'Destination', 'Action'].map((header) => (
                <div
                  key={header}
                  style={{
                    ...editHeaderCellStyle,
                    textAlign: header === 'Action' ? 'center' : 'left',
                  }}
                >
                  {header}
                </div>
              ))}

              {/* rows */}
              {pageData.map((item, index) => {
                // rowIdx is just the index in the current page array
                const rowIdx = index;
                return (
                  <React.Fragment key={`${item.reportId}-${rowIdx}`}>
                    <div style={editRowCellStyle}>{item.reportName}</div>
                    {labelCell(mapReceive(item.receive))}
                    {labelCell(mapDestination(item.destination))}
                    <div style={{ ...editRowCellStyle, gap: 4, justifyContent: 'center' }}>
                      <Tooltip title="Detail" arrow>
                        <IconButton
                          aria-label="detail"
                          size="small"
                          sx={{ p: 0.25, fontSize: '0.95rem' }}
                          onClick={() => openDetail(rowIdx)}
                        >
                          <InfoOutlinedIcon fontSize="inherit" />
                        </IconButton>
                      </Tooltip>
                      <Tooltip title="Edit" arrow>
                        <span>
                          <IconButton
                            aria-label="edit"
                            size="small"
                            sx={{ p: 0.25, fontSize: '0.95rem' }}
                            onClick={() => openEdit(rowIdx)}
                            disabled={!isEditable}
                          >
                            <EditOutlinedIcon fontSize="inherit" />
                          </IconButton>
                        </span>
                      </Tooltip>
                      <Tooltip title="Remove" arrow>
                        <span>
                          <IconButton
                            aria-label="remove"
                            size="small"
                            sx={{ p: 0.25, fontSize: '0.95rem' }}
                            onClick={() => openDelete(rowIdx)}
                            disabled={!isEditable}
                          >
                            <DeleteOutlineIcon fontSize="inherit" />
                          </IconButton>
                        </span>
                      </Tooltip>
                    </div>
                  </React.Fragment>
                );
              })}
            </div>
          </div>

          {/* pagination (top) */}
          <div
            style={{
              marginBottom: '6px',
              display: 'flex',
              justifyContent: 'space-between',
              alignItems: 'center',
            }}
          >
            <Button
              variant="text"
              size="small"
              sx={{ fontSize: '0.68rem', padding: '2px 6px', minWidth: 'unset', textTransform: 'none' }}
              onClick={() => setPage((p) => Math.max(p - 1, 0))}
              disabled={page === 0}
            >
              ◀ Previous
            </Button>
            <Typography fontSize="0.72rem">
              Page {totalCount > 0 ? page + 1 : 0} of {pageCount}
            </Typography>
            <Button
              variant="text"
              size="small"
              sx={{ fontSize: '0.68rem', padding: '2px 6px', minWidth: 'unset', textTransform: 'none' }}
              onClick={() => setPage((p) => Math.min(p + 1, pageCount - 1))}
              disabled={page >= pageCount - 1}
            >
              Next ▶
            </Button>
          </div>

          {/* "New" button centered horizontally */}
          <div style={{ marginTop: '8px', display: 'flex', justifyContent: 'center' }}>
            <Button
              variant="contained"
              size="small"
              onClick={handleNewClick}
              disabled={!isEditable}
            >
              New
            </Button>
          </div>
        </CCardBody>
      </CCard>

      {/* detail/edit/new/delete window */}
      <ClientReportWindow
        open={modalOpen}
        mode={modalMode}
        row={currentRow}
        clientId={clientId}
        onClose={() => {
          setModalOpen(false);
          setDraftRow(null);
        }}
        onSave={handleSaveFromModal}     // 'edit' and 'new'
        onDelete={handleDeleteFromModal} // after successful DELETE (keeps dialog open)
      />
    </div>
  );
};

export default EditClientReport;
