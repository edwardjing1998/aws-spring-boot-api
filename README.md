import { useEffect, useRef, useState, useMemo } from 'react';
import { CCol, CRow, CCard, CCardBody } from '@coreui/react';
import './WebClientDirectory.scss';

import { AllCommunityModule, ModuleRegistry } from 'ag-grid-community';
import { AgGridReact } from 'ag-grid-react';
import { webClientDirectoryRows } from './data';
import WebClientDirectoryDialog from './WebClientDirectoryDialog';

// MUI icons
import { IconButton, Tooltip } from '@mui/material';
import EditOutlinedIcon from '@mui/icons-material/EditOutlined';
import VisibilityOutlinedIcon from '@mui/icons-material/VisibilityOutlined';
import DeleteOutlineOutlinedIcon from '@mui/icons-material/DeleteOutlineOutlined';

ModuleRegistry.registerModules([AllCommunityModule]);

const defaultColDef = {
  flex: 1,
  filter: true,
  floatingFilter: false,
  sortable: true,
  resizable: true,
};

const WebClientDirectory = () => {
  const [rowData, setRowData] = useState([]);
  const [pageSize] = useState(10);
  const gridApi = useRef(null);

  // messages (optional)
  const [successMessage, setSuccessMessage] = useState('');
  const [validationError, setValidationError] = useState('');

  // Dialog state
  const [dialogOpen, setDialogOpen] = useState(false);
  const [dialogMode, setDialogMode] = useState('review'); // 'review' | 'delete' | 'addEdit'
  const [dialogTitle, setDialogTitle] = useState('Web Client Directory Review');
  const [dialogInitial, setDialogInitial] = useState(null);
  const [pendingDeleteRow, setPendingDeleteRow] = useState(null);

  // Init from local JSON
  useEffect(() => {
    const normalized = (webClientDirectoryRows || []).map((r) => {
      const userId = (r.userId ?? '').trimEnd();
      const clientId = (r.clientId ?? '').trim();
      const pathTx = r.pathTx ?? '';
      const webReportId = Number(r.webReportId) || 0;

      return {
        userId,
        clientId,
        pathTx,
        webReportIdStr: String(webReportId).padStart(5, '0'),
        webReportId,
      };
    });

    setRowData(normalized);
  }, []);

  const buildInitialFromRow = (row) => ({
    userId: row?.userId ?? '',
    clientId: row?.clientId ?? '',
    pathTx: row?.pathTx ?? '',
    webReportId: Number(row?.webReportId) || 0,
  });

  // --- EDIT -> addEdit mode with Cancel/Update buttons in dialog
  const openEditDialogForRow = (row) => {
    const init = buildInitialFromRow(row);
    if (!init.userId) return;
    setDialogInitial(init);
    setDialogMode('addEdit');
    setDialogTitle('Edit Web Client Directory');
    setDialogOpen(true);
  };

  const openReviewDialogForRow = (row) => {
    const init = buildInitialFromRow(row);
    if (!init.userId) return;
    setDialogInitial(init);
    setDialogMode('review');
    setDialogTitle('Web Client Directory Review');
    setDialogOpen(true);
  };

  const openDeleteDialogForRow = (row) => {
    const init = buildInitialFromRow(row);
    if (!init.userId) return;
    setDialogInitial(init);
    setPendingDeleteRow(init);
    setDialogMode('delete');
    setDialogTitle('Delete Web Client Directory');
    setDialogOpen(true);
  };

  // Update handler from dialog
  const handleConfirmUpdate = async (updated) => {
    // Find the original entry using the dialogInitial (acts as the original key)
    const orig = dialogInitial;
    if (!orig) return;

    setSuccessMessage('');
    setValidationError('');

    try {
      setRowData((prev) => {
        const idx = prev.findIndex(
          (r) =>
            r.userId === orig.userId &&
            r.clientId === orig.clientId &&
            Number(r.webReportId) === Number(orig.webReportId)
        );
        const next = [...prev];
        const newRow = {
          userId: (updated.userId ?? '').trimEnd(),
          clientId: (updated.clientId ?? '').trim(),
          pathTx: (updated.pathTx ?? '').trim(),
          webReportId: Number(updated.webReportId) || 0,
          webReportIdStr: String(Number(updated.webReportId) || 0).padStart(5, '0'),
        };
        if (idx >= 0) next[idx] = newRow;
        else next.unshift(newRow);
        return next;
      });

      setDialogOpen(false);
      setDialogInitial(null);
      setSuccessMessage('Updated successfully!');
    } catch (err) {
      setValidationError('Failed to update (local).');
    }
  };

  const handleConfirmDelete = async () => {
    setSuccessMessage('');
    setValidationError('');
    const target = pendingDeleteRow;
    if (!target) return;

    try {
      setRowData((prev) =>
        prev.filter(
          (r) =>
            !(
              r.userId === target.userId &&
              r.clientId === target.clientId &&
              Number(r.webReportId) === Number(target.webReportId)
            )
        )
      );
      setPendingDeleteRow(null);
      setSuccessMessage('Deleted successfully!');
      setDialogOpen(false);
    } catch (err) {
      setValidationError('Failed to delete (local).');
    }
  };

  // ——— Columns (Action column included) ———
  const columnDefs = useMemo(
    () => [
      { field: 'webReportIdStr', headerName: 'Report ID', minWidth: 120 },
      { field: 'userId', headerName: 'User ID', minWidth: 140 },
      { field: 'clientId', headerName: 'Client ID', minWidth: 120 },
      { field: 'pathTx', headerName: 'x500 ID', minWidth: 160 },
      {
        headerName: 'Action',
        field: 'action',
        minWidth: 160,
        maxWidth: 200,
        sortable: false,
        filter: false,
        cellClass: 'ag-cell-center',
        cellRenderer: (p) => {
          const row = p.data;
          const onEdit = (e) => { e.stopPropagation(); openEditDialogForRow(row); };
          const onReview = (e) => { e.stopPropagation(); openReviewDialogForRow(row); };
          const onDelete = (e) => { e.stopPropagation(); openDeleteDialogForRow(row); };
          return (
            <div style={{ display: 'flex', gap: 8, alignItems: 'center', justifyContent: 'center' }}>
              <Tooltip title="Edit">
                <span>
                  <IconButton size="small" onClick={onEdit} sx={{ color: 'gray' }}>
                    <EditOutlinedIcon fontSize="inherit" />
                  </IconButton>
                </span>
              </Tooltip>
              <Tooltip title="Review">
                <span>
                  <IconButton size="small" onClick={onReview} sx={{ color: 'gray' }}>
                    <VisibilityOutlinedIcon fontSize="inherit" />
                  </IconButton>
                </span>
              </Tooltip>
              <Tooltip title="Delete">
                <span>
                  <IconButton size="small" onClick={onDelete} sx={{ color: 'gray' }}>
                    <DeleteOutlineOutlinedIcon fontSize="inherit" />
                  </IconButton>
                </span>
              </Tooltip>
            </div>
          );
        },
      },
    ],
    []
  );

  const onGridReady = (params) => {
    gridApi.current = params.api;
  };

  return (
    <div>
      <CRow className="mb-3">
        <CCol xs={12}>
          <CCard>
            <CCardBody>
              <div className="ag-grid-container ag-theme-quartz" style={{ height: '600px' }}>
                <AgGridReact
                  columnDefs={columnDefs}
                  defaultColDef={defaultColDef}
                  rowData={rowData}
                  rowModelType="clientSide"
                  pagination
                  paginationPageSize={pageSize}
                  paginationPageSizeSelector={[10, 20, 50, 100]}
                  onGridReady={onGridReady}
                />
              </div>
            </CCardBody>
          </CCard>
        </CCol>
      </CRow>

      {/* Optional messages */}
      {successMessage && (
        <CRow className="mb-3">
          <CCol xs={12}>
            <div className="text-success">{successMessage}</div>
          </CCol>
        </CRow>
      )}
      {validationError && (
        <CRow className="mb-3">
          <CCol xs={12}>
            <div className="text-danger">{validationError}</div>
          </CCol>
        </CRow>
      )}

      {/* Dialog */}
      <WebClientDirectoryDialog
        open={dialogOpen}
        mode={dialogMode}                // 'review' | 'delete' | 'addEdit'
        title={dialogTitle}
        initialData={dialogInitial}
        onClose={() => { setDialogOpen(false); setPendingDeleteRow(null); }}
        onConfirmDelete={handleConfirmDelete}
        onConfirmUpdate={handleConfirmUpdate}  // <— NEW
      />
    </div>
  );
};

export default WebClientDirectory;



import React, { useEffect, useState } from 'react';
import { Dialog, DialogTitle, DialogContent, DialogActions, Button } from '@mui/material';
import { CFormInput } from '@coreui/react';

const WebClientDirectoryDialog = ({
  open,
  mode = 'review',              // 'review' | 'delete' | 'addEdit'
  title,
  initialData,                   // { userId, clientId, pathTx, webReportId }
  onClose,
  onConfirmDelete,
  onConfirmUpdate,               // (payload) => void
}) => {
  const [form, setForm] = useState({
    userId: '',
    clientId: '',
    pathTx: '',
    webReportId: 0,
  });

  useEffect(() => {
    if (!open) return;
    setForm({
      userId: initialData?.userId ?? '',
      clientId: initialData?.clientId ?? '',
      pathTx: initialData?.pathTx ?? '',
      webReportId: Number(initialData?.webReportId) || 0,
    });
  }, [open, initialData]);

  const isReadOnly = mode === 'review';
  const isDelete = mode === 'delete';
  const isAddEdit = mode === 'addEdit';

  const handleUpdate = () => {
    if (!onConfirmUpdate) return;
    onConfirmUpdate({
      userId: form.userId,
      clientId: form.clientId,
      pathTx: form.pathTx,
      webReportId: Number(form.webReportId) || 0,
    });
  };

  return (
    <Dialog open={open} onClose={onClose} PaperProps={{ className: 'wcd-dialog' }}>
      <DialogTitle>
        {title ?? (isAddEdit ? 'Edit Web Client Directory' : isDelete ? 'Delete Web Client Directory' : 'Web Client Directory Review')}
      </DialogTitle>

      <DialogContent dividers>
        {isDelete ? (
          <div style={{ padding: '6px 2px' }}>
            Are you sure you want to delete this record?
            <div style={{ marginTop: 8, fontSize: 13, opacity: 0.8 }}>
              <div><strong>User Id:</strong> {initialData?.userId}</div>
              <div><strong>Client Id:</strong> {initialData?.clientId}</div>
              <div><strong>x500 Id:</strong> {initialData?.pathTx}</div>
              <div><strong>Report ID:</strong> {String(initialData?.webReportId ?? '').padStart(5, '0')}</div>
            </div>
          </div>
        ) : (
          <div style={{ display: 'grid', gap: 12, minWidth: 420 }}>
            <div>
              <label className="form-label">User Id</label>
              <CFormInput
                value={form.userId}
                onChange={(e) => setForm((p) => ({ ...p, userId: e.target.value }))}
                disabled={isReadOnly}
              />
            </div>
            <div>
              <label className="form-label">Client Id</label>
              <CFormInput
                value={form.clientId}
                onChange={(e) => setForm((p) => ({ ...p, clientId: e.target.value }))}
                disabled={isReadOnly}
              />
            </div>
            <div>
              <label className="form-label">x500 Id</label>
              <CFormInput
                value={form.pathTx}
                onChange={(e) => setForm((p) => ({ ...p, pathTx: e.target.value }))}
                disabled={isReadOnly}
              />
            </div>
            <div>
              <label className="form-label">Report ID</label>
              <CFormInput
                type="number"
                value={form.webReportId}
                onChange={(e) => setForm((p) => ({ ...p, webReportId: e.target.value }))}
                disabled={isReadOnly}
              />
            </div>
          </div>
        )}
      </DialogContent>

      <DialogActions>
        {isDelete ? (
          <>
            <Button variant="outlined" size="small" onClick={onClose}>Cancel</Button>
            <Button variant="contained" size="small" color="error" onClick={onConfirmDelete}>Delete</Button>
          </>
        ) : isAddEdit ? (
          <>
            <Button variant="outlined" size="small" onClick={onClose}>Cancel</Button>
            <Button variant="contained" size="small" onClick={handleUpdate}>Update</Button>
          </>
        ) : (
          // review
          <Button variant="contained" size="small" onClick={onClose}>Close</Button>
        )}
      </DialogActions>
    </Dialog>
  );
};

export default WebClientDirectoryDialog;






