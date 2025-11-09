// EditClientReport.jsx
import React, { useEffect, useMemo, useState } from 'react';
import { Typography, Button, IconButton, Tooltip } from '@mui/material';
import InfoOutlinedIcon from '@mui/icons-material/InfoOutlined';
import EditOutlinedIcon from '@mui/icons-material/EditOutlined';
import DeleteOutlineIcon from '@mui/icons-material/DeleteOutline';
import { CCard, CCardBody } from '@coreui/react';

import ClientReportWindow from './utils/ClientReportWindow';

const PAGE_SIZE = 10;

const EditClientReport = ({ selectedGroupRow, isEditable, onDataChange }) => {
  const [tableData, setTableData] = useState([]);
  const [page, setPage] = useState(0);

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
        (x.emailBodyTx ?? '') === (y.emailBodyTx ?? '')
      );
    });

  const rowToOption = (r) => ({
    reportDetails: { queryName: r.reportName ?? '', reportId: r.reportId ?? null },
    reportId: r.reportId ?? null,
    receiveFlag: String(r.receive) === '1',
    outputTypeCd: Number(r.destination ?? 0),
    fileTypeCd: Number(r.fileText ?? 0),
    emailFlag: Number(r.email ?? 0),
    reportPasswordTx: r.password ?? '',
    emailBodyTx: r.emailBodyTx ?? '',
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
        (x.emailBodyTx ?? '') === (y.emailBodyTx ?? '')
      );
    });

  const pushUp = (rows) => {
    if (typeof onDataChange !== 'function') return;
    const nextOptions = rows.map(rowToOption);
    const existing = Array.isArray(selectedGroupRow?.reportOptions)
      ? selectedGroupRow.reportOptions
      : [];
    if (optionsEqual(nextOptions, existing)) return; // avoid no-op loops
    onDataChange({ ...(selectedGroupRow ?? {}), reportOptions: nextOptions });
  };
  // ===================================================================

  // pagination
  const pageCount = useMemo(
    () => Math.ceil((tableData?.length || 0) / PAGE_SIZE),
    [tableData]
  );
  const pageData = useMemo(
    () => tableData.slice(page * PAGE_SIZE, (page + 1) * PAGE_SIZE),
    [tableData, page]
  );

  // load/normalize incoming data from parent
  useEffect(() => {
    const options = Array.isArray(selectedGroupRow?.reportOptions)
      ? selectedGroupRow.reportOptions
      : [];

    const nextRows = options.map((option) => ({
      reportName: option?.reportDetails?.queryName || '',
      reportId: option?.reportDetails?.reportId ?? option?.reportId ?? null,
      receive: option?.receiveFlag ? '1' : '2', // 1=Yes, 2=No
      destination:
        option?.outputTypeCd !== undefined ? String(option.outputTypeCd) : '0', // 0=None,1=File,2=Print
      fileText:
        option?.fileTypeCd !== undefined ? String(option.fileTypeCd) : '0',     // 0=None,1=Text,2=Excel
      email:
        option?.emailFlag === 1 ? '1' : option?.emailFlag === 2 ? '2' : '0',   // 0=None,1=Email,2=Web
      password: option?.reportPasswordTx || '',
      emailBodyTx: option?.emailBodyTx || '',
    }));

    // ✅ Guard to avoid update loops if parent sends identical data
    setTableData((prev) => (rowsEqual(prev, nextRows) ? prev : nextRows));

    const lastPage = Math.max(Math.ceil(nextRows.length / PAGE_SIZE) - 1, 0);
    setPage((p) => Math.min(p, lastPage));
  }, [selectedGroupRow]); // intentionally depends on object, we guard equality above

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
    setTableData((prev) => {
      const next = prev.filter((_, i) => i !== rowIdx);
      const lastPage = Math.max(Math.ceil(next.length / PAGE_SIZE) - 1, 0);
      setPage((p) => Math.min(p, lastPage));
      // bubble to parent only if changed
      if (!rowsEqual(prev, next)) pushUp(next);
      return next;
    });
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
      };
      setTableData((prev) => {
        const next = [...prev, normalized];
        const lastPage = Math.max(Math.ceil(next.length / PAGE_SIZE) - 1, 0);
        setPage(lastPage);
        if (!rowsEqual(prev, next)) pushUp(next);
        return next;
      });
      // keep dialog open
    } else {
      // updating existing row
      setTableData((prev) => {
        const next = [...prev];
        next[modalRowIdx] = {
          ...next[modalRowIdx],
          ...updatedRow,
          reportId: updatedRow?.reportId ?? next[modalRowIdx].reportId ?? null,
          receive:
            updatedRow?.receive != null
              ? String(updatedRow.receive)
              : String(next[modalRowIdx].receive ?? '0'),
          destination:
            updatedRow?.destination != null
              ? String(updatedRow.destination)
              : String(next[modalRowIdx].destination ?? '0'),
          fileText:
            updatedRow?.fileText != null
              ? String(updatedRow.fileText)
              : String(next[modalRowIdx].fileText ?? '0'),
          email:
            updatedRow?.email != null
              ? String(updatedRow.email)
              : String(next[modalRowIdx].email ?? '0'),
          password: updatedRow?.password ?? next[modalRowIdx].password ?? '',
          emailBodyTx: updatedRow?.emailBodyTx ?? next[modalRowIdx].emailBodyTx ?? '',
        };
        if (!rowsEqual(prev, next)) pushUp(next);
        return next;
      });
      // keep dialog open
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

  // derive a clientId to pass down
  const clientId =
    selectedGroupRow?.client ||
    selectedGroupRow?.clientId ||
    selectedGroupRow?.billingSp ||
    '';

  return (
    <div style={{ padding: '10px' }}>
      <CCard>
        <CCardBody>
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
              disabled={page === 0 || pageCount === 0}
            >
              ◀ Previous
            </Button>
            <Typography fontSize="0.72rem">
              Page {tableData.length > 0 ? page + 1 : 0} of {tableData.length > 0 ? pageCount : 0}
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

          {/* table */}
          <div style={{ height: '320px', marginBottom: '4px', marginTop: '2px' }}>
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
                const rowIdx = page * PAGE_SIZE + index;
                return (
                  <React.Fragment key={`${item.reportName}-${rowIdx}`}>
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
