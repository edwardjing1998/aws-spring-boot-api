// EditClientReport.jsx
import React, { useEffect, useMemo, useState } from 'react';
import { Typography, Button, IconButton, Tooltip } from '@mui/material';
import InfoOutlinedIcon from '@mui/icons-material/InfoOutlined';
import EditOutlinedIcon from '@mui/icons-material/EditOutlined';
import DeleteOutlineIcon from '@mui/icons-material/DeleteOutline';
import { CCard, CCardBody } from '@coreui/react';

import ClientReportWindow from './utils/ClientReportWindow';

const PAGE_SIZE = 8;

const EditClientReport = ({ selectedGroupRow, isEditable, onDataChange }) => {
  // tableData now holds JUST the current page from API
  const [tableData, setTableData] = useState([]);
  const [page, setPage] = useState(0);
  
  // NEW: Trigger for re-fetching data without changing page
  const [refreshKey, setRefreshKey] = useState(0);

  // Derive clientId and total count from props
  const clientId =
    selectedGroupRow?.client ||
    selectedGroupRow?.clientId ||
    selectedGroupRow?.billingSp ||
    '';
    
  const totalCount = selectedGroupRow?.reportOptionTotal || 0;

  // modal state
  const [modalOpen, setModalOpen] = useState(false);
  const [modalMode, setModalMode] = useState('detail'); // 'detail' | 'edit' | 'new' | 'delete'
  const [modalRowIdx, setModalRowIdx] = useState(null); // null => creating new
  const [draftRow, setDraftRow] = useState(null);       // snapshot after delete or "new"

  // ================= Helpers to compare rows/options =================
  const rowsEqual = (a = [], b = []) =>
    a.length === b.length &&
    a.every((x, i) => {
      const y = b[i] || {};
      return (
        (x.reportName ?? '') === (y.reportName ?? '') &&
        (x.reportId ?? null) === (y.reportId ?? null) &&
        String(x.receive ?? '0') === String(y.receive ?? '0') &&
        String(x.destination ?? '0') === String(y.destination ?? '0') &&
        String(x.fileText ?? '0') === String(y.fileText ?? '0') &&
        String(x.email ?? '0') === String(y.email ?? '0') &&
        (x.password ?? '') === (y.password ?? '') &&
        (x.emailBodyTx ?? '') === (y.emailBodyTx ?? '') &&
        String(x.fileExt ?? '') === String(y.fileExt ?? '')
      );
    });

  // take an internal row and build the object stored in parent.selectedGroupRow.reportOptions
  const rowToOption = (r) => ({
    reportDetails: {
      queryName: r.reportName ?? '',
      reportId: r.reportId ?? null,
      fileExt: r.fileExt ?? '',        // ⬅️ nested here for ClientReports
    },
    reportId: r.reportId ?? null,
    receiveFlag: String(r.receive) === '1',
    outputTypeCd: Number(r.destination ?? 0),
    fileTypeCd: Number(r.fileText ?? 0),
    emailFlag: Number(r.email ?? 0),
    reportPasswordTx: r.password ?? '',
    emailBodyTx: r.emailBodyTx ?? '',
    fileExt: r.fileExt ?? '',          // ⬅️ optional top-level copy
  });

  const optionsEqual = (a = [], b = []) =>
    a.length === b.length &&
    a.every((x, i) => {
      const y = b[i] || {};
      const xid = x.reportDetails?.reportId ?? x.reportId ?? null;
      const yid = y.reportDetails?.reportId ?? y.reportId ?? null;
      return (
        (x.reportDetails?.queryName ?? '') === (y.reportDetails?.queryName ?? '') &&
        xid === yid &&
        !!x.receiveFlag === !!y.receiveFlag &&
        Number(x.outputTypeCd ?? 0) === Number(y.outputTypeCd ?? 0) &&
        Number(x.fileTypeCd ?? 0) === Number(y.fileTypeCd ?? 0) &&
        Number(x.emailFlag ?? 0) === Number(y.emailFlag ?? 0) &&
        (x.reportPasswordTx ?? '') === (y.reportPasswordTx ?? '') &&
        (x.emailBodyTx ?? '') === (y.emailBodyTx ?? '') &&
        String(x.fileExt ?? '') === String(y.fileExt ?? '')
      );
    });

  const pushUp = (rows) => {
    if (typeof onDataChange !== 'function') return;
    const nextOptions = rows.map(rowToOption);
    // Note: This logic might need review if you are only pushing up ONE page of data
    // to a parent that expects ALL data. For now, preserving behavior for the visible page.
    onDataChange({ ...(selectedGroupRow ?? {}), reportOptions: nextOptions });
  };
  // ===================================================================

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
            const options = await resp.json();
            
            // Normalize incoming data to table row format
            const nextRows = Array.isArray(options) ? options.map((option) => ({
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

            setTableData(nextRows);
        } else {
            console.error("Failed to fetch client reports");
            setTableData([]);
        }
      } catch (error) {
        console.error("Error fetching client reports:", error);
        setTableData([]);
      }
    };

    fetchData();
  }, [clientId, page, refreshKey]); // ✅ Added refreshKey to dependencies

  // pagination metrics
  const pageCount = Math.ceil(totalCount / PAGE_SIZE);
  // Since tableData IS the current page now, we just use it directly
  const pageData = tableData;

  // styles
  const rowCellStyle = {
    backgroundColor: 'white',
    fontSize: '0.72rem',
    lineHeight: 1.1,
    padding: '2px 6px',
    borderBottom: '1px dotted #ddd',
    display: 'flex',
    alignItems: 'center',
    whiteSpace: 'nowrap',
    overflow: 'hidden',
    textOverflow: 'ellipsis',
  };
  const headerCellStyle = {
    backgroundColor: '#f0f0f0',
    fontWeight: 'bold',
    fontSize: '0.75rem',
    padding: '2px 6px',
    borderBottom: '1px dotted #ccc',
  };
  const labelCell = (text, align = 'center') => (
    <div style={{ ...rowCellStyle, justifyContent: align }}>
      <Typography noWrap sx={{ fontSize: '0.72rem', lineHeight: 1.1 }}>
        {text}
      </Typography>
    </div>
  );

  // label mappers
  const mapReceive = (v) => (v === '1' ? 'Yes' : v === '2' ? 'No' : 'None');
  const mapDestination = (v) => (v === '1' ? 'File' : v === '2' ? 'Print' : 'None');

  // actions -> detail/edit/delete
  // Note: rowIdx here is index within the PAGE (0-7), not global index
  const openDetail = (rowIdx) => {
    setModalMode('detail');
    setModalRowIdx(rowIdx);
    setDraftRow(null);
    setModalOpen(true);
  };
  const openEdit = (rowIdx) => {
    setModalMode('edit');
    setModalRowIdx(rowIdx);
    setDraftRow(null);
    setModalOpen(true);
  };
  const openDelete = (rowIdx) => {
    setModalMode('delete');
    setModalRowIdx(rowIdx);
    setDraftRow(null);
    setModalOpen(true);
  };

  // actual deletion logic — KEEP DIALOG OPEN
  const handleRemove = (rowIdx) => {
    // For server-side deletion, you would ideally call API here.
    // For now, triggering a refresh to sync.
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
  const handleSaveFromModal = (updatedRow) => {
    if (modalRowIdx == null) {
      // creating a new row
      const normalized = {
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
      
      // Pass the new data up (assuming parent handles the API save)
      // Note: pushUp currently takes an array. With server paging, logic might differ.
      // Assuming parent handles the insertion based on this call:
      pushUp([...tableData, normalized]); 

      // ✅ AUTO PAGING LOGIC
      // 1. Calculate new page index (Last page)
      // Assuming the save was successful and count increased by 1
      const newTotalCount = totalCount + 1;
      const newLastPage = Math.max(0, Math.ceil(newTotalCount / PAGE_SIZE) - 1);
      
      // 2. Set Page to Last Page
      setPage(newLastPage);

      // 3. Trigger Refresh (to fetch the new data at that page)
      setRefreshKey(prev => prev + 1);

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
                    ...headerCellStyle,
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
                    <div style={rowCellStyle}>{item.reportName}</div>
                    {labelCell(mapReceive(item.receive))}
                    {labelCell(mapDestination(item.destination))}
                    <div style={{ ...rowCellStyle, gap: 4, justifyContent: 'center' }}>
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
              disabled={page >= pageCount - 1 || pageCount === 0}
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
