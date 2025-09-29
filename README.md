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
  const [pageSize, setPageSize] = useState(10);
  const gridApi = useRef(null);

  // messages (optional)
  const [successMessage, setSuccessMessage] = useState('');
  const [validationError, setValidationError] = useState('');

  // Dialog state
  const [dialogOpen, setDialogOpen] = useState(false);
  const [dialogMode, setDialogMode] = useState('review'); // 'review' | 'delete'
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

  const openEditDialogForRow = (row) => {
    const init = buildInitialFromRow(row);
    if (!init.userId) return;
    setDialogInitial(init);
    setDialogMode('review'); // reuse read-only dialog as "Edit" viewer
    setDialogTitle('Web Client Directory Edit');
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
    } catch (err) {
      setValidationError('Failed to delete (local).');
    }
  };

  // ——— Columns (Action column included) ———
  const columnDefs = useMemo(() => ([
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
  ]), []);

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
                  pagination={true}
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
        mode={dialogMode}                // 'review' | 'delete'
        title={dialogTitle}
        initialData={dialogInitial}
        onClose={() => { setDialogOpen(false); setPendingDeleteRow(null); }}
        onConfirmDelete={handleConfirmDelete}
      />
    </div>
  );
};

export default WebClientDirectory;
