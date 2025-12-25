// src/views/sys-prin-configuration/EditClientReport.tsx

import React, { useEffect, useMemo, useState } from 'react';
import { Typography, Button, IconButton, Tooltip } from '@mui/material';
import InfoOutlinedIcon from '@mui/icons-material/InfoOutlined';
import EditOutlinedIcon from '@mui/icons-material/EditOutlined';
import DeleteOutlineIcon from '@mui/icons-material/DeleteOutline';
import { CCard, CCardBody } from '@coreui/react';

import ClientReportWindow from './ClientReportWindow';
import { ClientReportRow, EditClientReportProps } from './EditClientReport.types';
import { editRowCellStyle, editHeaderCellStyle } from './EditClientReport.styles';
import { fetchEditClientReport } from './ClientReport.service';

const PAGE_SIZE = 8;

const EditClientReport: React.FC<EditClientReportProps> = ({ selectedGroupRow, isEditable, onDataChange }) => {
  const [tableData, setTableData] = useState<ClientReportRow[]>([]);
  const [page, setPage] = useState<number>(0);

  const [refreshKey, setRefreshKey] = useState<number>(0);
  const [localCountAdjustment, setLocalCountAdjustment] = useState<number>(0);

  const clientId =
    selectedGroupRow?.client ||
    selectedGroupRow?.clientId ||
    selectedGroupRow?.billingSp ||
    '';

  const baseTotalCount = selectedGroupRow?.reportOptionTotal || 0;
  const totalCount = Math.max(0, baseTotalCount + localCountAdjustment);

  useEffect(() => {
    setLocalCountAdjustment(0);
  }, [baseTotalCount, clientId]);

  const [modalOpen, setModalOpen] = useState<boolean>(false);
  const [modalMode, setModalMode] = useState<'detail' | 'edit' | 'new' | 'delete'>('detail');
  const [modalRowIdx, setModalRowIdx] = useState<number | null>(null);
  const [draftRow, setDraftRow] = useState<ClientReportRow | null>(null);

  useEffect(() => {
    if (!clientId) return;

    const fetchData = async () => {
      try {
        const nextRows = await fetchEditClientReport(clientId, page, PAGE_SIZE);
        setTableData(nextRows);
      } catch (error) {
        console.error("Error fetching client reports:", error);
        setTableData([]);
      }
    };

    fetchData();
  }, [clientId, page, refreshKey]);

  const pageCount = Math.ceil(totalCount / PAGE_SIZE);
  const pageData = tableData;

  // ✅ Helper: tell parent the new total (so pagination stays correct after add/delete)
  const notifyParentTotal = (newTotal: number) => {
    if (!clientId) return;
    // If your parent expects a different payload shape, only adjust this line.
    onDataChange?.(clientId, { reportOptionTotal: Math.max(0, newTotal) } as any);
  };

  const labelCell = (text: string, align: 'left' | 'center' | 'right' = 'center') => (
    <div style={{ ...editRowCellStyle, justifyContent: align }}>
      <Typography noWrap sx={{ fontSize: '0.72rem', lineHeight: 1.1 }}>
        {text}
      </Typography>
    </div>
  );

  const mapReceive = (v: string) => (v === '1' ? 'Yes' : v === '2' ? 'No' : 'None');
  const mapDestination = (v: string) => (v === '1' ? 'File' : v === '2' ? 'Print' : 'None');

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

  // ✅ Delete: update local + parent total + page index safely
  const handleRemove = (rowIdx: number) => {
    const nextTotal = Math.max(0, totalCount - 1);

    // optimistic total update
    setLocalCountAdjustment(prev => prev - 1);

    // ✅ update parent immediately so pageCount becomes correct
    notifyParentTotal(nextTotal);

    // ✅ if we deleted the last item on the last page, move back one page
    const nextPageCount = Math.ceil(nextTotal / PAGE_SIZE); // could be 0
    const maxPageIdx = Math.max(0, nextPageCount - 1);

    setPage((p) => Math.min(p, maxPageIdx));

    // refresh current page data from server
    setRefreshKey(prev => prev + 1);
  };

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
      fileExt: '',
    });
    setModalOpen(true);
  };

  // ✅ Add: update local + parent total (and keep your current paging behavior)
  const handleSaveFromModal = (updatedRow: Partial<ClientReportRow>) => {
    if (modalRowIdx == null) {
      const normalized: ClientReportRow = {
        reportName: updatedRow?.reportName ?? '',
        reportId: updatedRow?.reportId ?? null,
        receive: updatedRow?.receive != null ? String(updatedRow.receive) : '0',
        destination: updatedRow?.destination != null ? String(updatedRow.destination) : '0',
        fileText: updatedRow?.fileText != null ? String(updatedRow.fileText) : '0',
        email: updatedRow?.email != null ? String(updatedRow.email) : '0',
        password: updatedRow?.password ?? '',
        emailBodyTx: updatedRow?.emailBodyTx ?? '',
        fileExt: updatedRow?.fileExt ?? '',
      };

      const nextTotal = totalCount + 1;

      // optimistic total update
      setLocalCountAdjustment(prev => prev + 1);

      // ✅ update parent immediately so pageCount becomes correct
      notifyParentTotal(nextTotal);

      const currentLastPageIdx = Math.max(0, Math.ceil(totalCount / PAGE_SIZE) - 1);
      const isLastPage = page === currentLastPageIdx;

      if (isLastPage) {
        if (tableData.length < PAGE_SIZE) {
          setTableData(prev => [...prev, normalized]);
        } else {
          setPage(page + 1);
          setTableData([normalized]);
        }
      } else {
        // not on last page, keep view
      }

      // keep dialog open
    } else {
      // edit existing row -> refresh
      setRefreshKey(prev => prev + 1);
    }
  };

  const handleDeleteFromModal = () => {
    if (modalRowIdx == null) return;
    const snapshot = tableData[modalRowIdx] || null;
    setDraftRow(snapshot);
    setModalRowIdx(null);
    handleRemove(modalRowIdx);
  };

  const currentRow = modalRowIdx == null ? draftRow : tableData[modalRowIdx] || null;

  return (
    <div style={{ padding: '10px' }}>
      <CCard>
        <CCardBody>
          <div style={{ height: '280px', marginBottom: '4px', marginTop: '2px' }}>
            <div
              style={{
                display: 'grid',
                gridTemplateColumns: '4fr 1fr 1fr 3fr',
                columnGap: '4px',
              }}
            >
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

              {pageData.map((item, index) => {
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

      <ClientReportWindow
        open={modalOpen}
        mode={modalMode}
        row={currentRow}
        clientId={clientId}
        onClose={() => {
          setModalOpen(false);
          setDraftRow(null);
        }}
        onSave={handleSaveFromModal}
        onDelete={handleDeleteFromModal}
      />
    </div>
  );
};

export default EditClientReport;
