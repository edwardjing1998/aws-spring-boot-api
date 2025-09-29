import { useEffect, useRef, useState, useMemo } from 'react';
import {
  CFormInput, CCol, CFormCheck, CRow, CCard, CFormSelect, CCardBody
} from '@coreui/react';
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

const initialRef = {
  sUserId: '',
  sClientId: '',
  sWebDirectoryPath: '',
  iWebReportid: '',
};

const WebClientDirectory = () => {
  const [reportIdList, setReportIdList] = useState([]);
  const [rowData, setRowData] = useState([]);

  const [data, setData] = useState({
    userId: '',
    clientId: '',
    pathTx: '',
    webReportId: '',
    administrator: false,
  });
  const [ref, setRef] = useState(initialRef);

  const [successMessage, setSuccessMessage] = useState('');
  const [validationError, setValidationError] = useState('');
  const [pageSize, setPageSize] = useState(10);

  const gridApi = useRef(null);

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

    const uniqueIds = Array.from(new Set(normalized.map((r) => r.webReportId))).sort((a, b) => a - b);
    const dropdown = [{}, ...uniqueIds.map((id) => ({ webReportId: id, reportName: '' }))];
    setReportIdList(dropdown);
  }, []);

  const handleTableRowClick = (item) => {
    setTimeout(() => {
      setData({
        userId: item.userId ?? '',
        clientId: item.clientId ?? '',
        pathTx: item.pathTx ?? '',
        webReportId: String(item.webReportId ?? ''),
        administrator: (item.clientId ?? '').trim() === '' ? true : false,
      });
      setRef({
        sUserId: item.userId ?? '',
        sClientId: item.clientId ?? '',
        sWebDirectoryPath: item.pathTx ?? '',
        iWebReportid: String(item.webReportId ?? ''),
      });
    }, 0);
  };

  const isReportIdSelectDisabled =
    data.userId === ref.sUserId &&
    data.clientId === ref.sClientId &&
    ref.sUserId !== '';

  const isAdminUserEnabled =
    (data.userId.trim() !== '' && data.clientId.trim() === '') ||
    (data.userId.trim() === '' && data.clientId.trim() === '');

  const isClientIdDisabled = data.administrator === true;

  useEffect(() => {
    if (data.clientId.trim() === '' && !data.administrator) {
      setData((prev) => ({ ...prev, administrator: false }));
    }
  }, [data.clientId]);

  const isChanged = () => {
    if (
      data.userId.trim() === ref.sUserId.trim() &&
      data.clientId.trim() === ref.sClientId.trim() &&
      data.pathTx.trim() === ref.sWebDirectoryPath.trim() &&
      String(data.webReportId) === String(ref.iWebReportid)
    ) {
      return false;
    }
    if (
      data.userId.trim().length > 0 &&
      data.pathTx.trim().length > 0 &&
      data.clientId.trim().length === 4 &&
      data.webReportId
    ) {
      return true;
    }
    if (
      data.userId.trim().length > 0 &&
      data.pathTx.trim().length > 0 &&
      data.clientId.trim().length === 0 &&
      data.webReportId &&
      data.administrator
    ) {
      return true;
    }
    return false;
  };

  const handleClear = () => {
    setData({
      userId: '',
      clientId: '',
      pathTx: '',
      webReportId: '',
      administrator: false,
    });
    setRef(initialRef);
    setSuccessMessage('');
    setValidationError('');
  };

  // ——— Dialog helpers ———
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
      handleClear();
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
                  onRowClicked={(e) => handleTableRowClick(e.data)}
                />
              </div>
            </CCardBody>
          </CCard>
        </CCol>
      </CRow>

      {/* Form */}
      <CRow className="align-items-center mb-3">
        <CCol xs={2}><label htmlFor="input1" className="form-label me-2">User Id:</label></CCol>
        <CCol xs={2}>
          <CFormInput
            id="input1"
            type="text"
            maxLength={8}
            value={data.userId}
            onChange={(e) => setData({ ...data, userId: e.target.value })}
          />
        </CCol>
        <CCol xs={2}><label htmlFor="input2" className="form-label me-2">Client Id:</label></CCol>
        <CCol xs={2}>
          <CFormInput
            id="input2"
            type="text"
            maxLength={4}
            value={data.clientId}
            onChange={(e) => setData({ ...data, clientId: e.target.value })}
            disabled={isClientIdDisabled}
          />
        </CCol>
        <CCol xs={2}><label htmlFor="checkbox1" className="form-label me-2">Administrator:</label></CCol>
        <CCol xs={2}>
          <CFormCheck
            id="checkbox1"
            checked={data.administrator}
            onChange={(e) => setData({ ...data, administrator: e.target.checked })}
            disabled={!isAdminUserEnabled}
          />
        </CCol>
      </CRow>

      <CRow className="mb-3">
        <CCol xs={2}><label className="form-label me-2">x500 Id:</label></CCol>
        <CCol xs={4}>
          <CFormInput
            id="input3"
            type="text"
            value={data.pathTx}
            onChange={(e) => setData({ ...data, pathTx: e.target.value })}
          />
        </CCol>
      </CRow>

      <CRow className="mb-3">
        <CCol xs={2}><label className="form-label me-2">Report ID:</label></CCol>
        <CCol>
          <CFormSelect
            name="reportIdList"
            value={String(Number(data.webReportId) || '')}
            onChange={(e) => setData({ ...data, webReportId: e.target.value })}
            disabled={isReportIdSelectDisabled}
          >
            {reportIdList.map((option, index) => (
              <option key={index} value={option.webReportId ?? ''}>
                {option.webReportId ? String(option.webReportId).padStart(5, '0') : ''} {'   '}
                {option.reportName || ''}
              </option>
            ))}
          </CFormSelect>
        </CCol>
      </CRow>

      {/* Removed the bottom row of Ok/Cancel/Update/Clear/Help buttons */}

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
