import { useEffect, useRef, useState } from 'react';
import {
  CButton, CFormInput, CCol, CFormCheck, CRow, CCard, CFormSelect, CCardBody
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
  const [selectedReportName, setSelectedReportName] = useState('');
  const [successMessage, setSuccessMessage] = useState('');
  const [validationError, setValidationError] = useState('');
  const [pageSize, setPageSize] = useState(10);

  const gridApi = useRef(null);

  // Dialog state
  const [dialogOpen, setDialogOpen] = useState(false);
  const [dialogMode, setDialogMode] = useState('review'); // 'review' | 'delete'
  const [dialogTitle, setDialogTitle] = useState('Web Client Directory Review');
  const [dialogInitial, setDialogInitial] = useState(null);

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
      setSelectedReportName(
        reportIdList.find((r) => r.webReportId === item.webReportId)?.reportName || ''
      );
    }, 0);
  };

  const isDeleteEnabled =
    data.userId === ref.sUserId &&
    data.clientId === ref.sClientId &&
    ref.sUserId !== '';

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

  // Update (local)
  const handleUpdate = async () => {
    setSuccessMessage('');
    setValidationError('');
    try {
      const targetKey = (r) =>
        r.userId === ref.sUserId &&
        r.clientId === ref.sClientId &&
        String(r.webReportId) === String(ref.iWebReportid);

      const webReportIdNum = Number(data.webReportId) || 0;

      setRowData((prev) => {
        const idx = prev.findIndex(targetKey);
        const updated = {
          userId: data.userId.trimEnd(),
          clientId: data.clientId.trim(),
          pathTx: data.pathTx.trim(),
          webReportId: webReportIdNum,
          webReportIdStr: String(webReportIdNum).padStart(5, '0'),
        };
        if (idx >= 0) {
          const copy = prev.slice();
          copy[idx] = updated;
          return copy;
        }
        return [updated, ...prev];
      });

      handleClear();
      setSuccessMessage('Updated successfully!');
    } catch (err) {
      setValidationError('Failed to update (local).');
    }
  };

  // Delete: via dialog
  const openDeleteDialog = () => {
    if (!isDeleteEnabled) return;
    setDialogInitial({
      userId: ref.sUserId,
      clientId: ref.sClientId,
      pathTx: ref.sWebDirectoryPath,
      webReportId: Number(ref.iWebReportid) || 0,
    });
    setDialogMode('delete');
    setDialogTitle('Delete Web Client Directory');
    setDialogOpen(true);
  };

  const handleConfirmDelete = async () => {
    setSuccessMessage('');
    setValidationError('');
    try {
      setRowData((prev) =>
        prev.filter(
          (r) =>
            !(
              r.userId === ref.sUserId &&
              r.clientId === ref.sClientId &&
              String(r.webReportId) === String(ref.iWebReportid)
            )
        )
      );
      setSuccessMessage('Deleted successfully!');
      handleClear();
    } catch (err) {
      setValidationError('Failed to delete (local).');
    }
  };

  const openReviewDialog = () => {
    if (!ref.sUserId) return;
    setDialogInitial({
      userId: ref.sUserId,
      clientId: ref.sClientId,
      pathTx: ref.sWebDirectoryPath,
      webReportId: Number(ref.iWebReportid) || 0,
    });
    setDialogMode('review');
    setDialogTitle('Web Client Directory Review');
    setDialogOpen(true);
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
    setSelectedReportName('');
    setSuccessMessage('');
    setValidationError('');
  };

  const columnDefs = [
    { field: 'webReportIdStr', headerName: 'Report ID', minWidth: 120 },
    { field: 'userId', headerName: 'User ID', minWidth: 140 },
    { field: 'clientId', headerName: 'Client ID', minWidth: 120 },
    { field: 'pathTx', headerName: 'x500 ID', minWidth: 160 },
  ];

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

      {successMessage && (
        <CRow className="mb-3">
          <CCol xs={4}>
            <label className="form-label me-2 text-success">{successMessage}</label>
          </CCol>
        </CRow>
      )}
      {validationError && (
        <CRow className="mb-3">
          <CCol xs={8}>
            <p className="text-danger">{validationError}</p>
          </CCol>
        </CRow>
      )}

      {/* Action row: icons + (optional) legacy buttons */}
      <CRow>
        <CCol className="d-flex justify-content-end align-items-center gap-2 flex-wrap">

          {/* Icons */}
          <div style={{ display: 'flex', alignItems: 'center', gap: 8, marginRight: 12 }}>
            <Tooltip title="Edit (Save changes)">
              <span>
                <IconButton
                  size="small"
                  onClick={handleUpdate}
                  disabled={!isChanged()}
                  sx={{ color: 'gray' }}
                >
                  <EditOutlinedIcon fontSize="inherit" />
                </IconButton>
              </span>
            </Tooltip>

            <Tooltip title="Review">
              <span>
                <IconButton
                  size="small"
                  onClick={openReviewDialog}
                  disabled={!ref.sUserId}
                  sx={{ color: 'gray' }}
                >
                  <VisibilityOutlinedIcon fontSize="inherit" />
                </IconButton>
              </span>
            </Tooltip>

            <Tooltip title="Delete">
              <span>
                <IconButton
                  size="small"
                  onClick={openDeleteDialog}
                  disabled={!isDeleteEnabled}
                  sx={{ color: 'gray' }}
                >
                  <DeleteOutlineOutlinedIcon fontSize="inherit" />
                </IconButton>
              </span>
            </Tooltip>
          </div>

          {/* (Optional) keep these; remove if you want icons-only */}
          <CButton color="primary" type="button" onClick={handleUpdate} disabled={!isChanged()}>Ok</CButton>
          <CButton type="button" color="primary" onClick={handleClear}>Cancel</CButton>
          <CButton type="button" color="primary" onClick={handleUpdate} disabled={!isChanged()}>Update</CButton>
          <CButton type="button" color="primary" onClick={handleClear}>Clear</CButton>
          <CButton type="button" color="primary">Help</CButton>
        </CCol>
      </CRow>

      {/* Dialog */}
      <WebClientDirectoryDialog
        open={dialogOpen}
        mode={dialogMode}                // 'review' | 'delete'
        title={dialogTitle}
        initialData={dialogInitial}
        onClose={() => setDialogOpen(false)}
        onConfirmDelete={handleConfirmDelete}
      />
    </div>
  );
};

export default WebClientDirectory;


import React from 'react';
import PropTypes from 'prop-types';
import { Dialog, DialogTitle, DialogContent, DialogActions, Button, IconButton } from '@mui/material';
import { CFormInput } from '@coreui/react';
// If you store icons elsewhere, adjust the import below:
import { CloseIcon } from '../../../assets/brand/svg-constants';

const Field = ({ label, value }) => (
  <div style={{
    display: 'grid',
    gridTemplateColumns: '140px 1fr',
    alignItems: 'center',
    gap: 8,
    marginBottom: 10
  }}>
    <label style={{ fontSize: 13 }}>{label}</label>
    <CFormInput value={value ?? ''} disabled />
  </div>
);

const WebClientDirectoryDialog = ({
  open,
  onClose,
  mode = 'review',            // 'review' | 'delete'
  title,
  initialData,
  onConfirmDelete,
}) => {
  const isDelete = mode === 'delete';
  const computedTitle =
    title ?? (isDelete ? 'Delete Web Client Directory' : 'Web Client Directory Review');

  const data = initialData || {
    userId: '',
    clientId: '',
    pathTx: '',
    webReportId: '',
  };

  const onDialogClose = (event, reason) => {
    if (reason === 'backdropClick' || reason === 'escapeKeyDown') return;
    onClose?.();
  };

  return (
    <Dialog open={open} onClose={onDialogClose} PaperProps={{ className: 'web-client-directory-dialog' }}>
      <DialogTitle>{computedTitle}</DialogTitle>
      <IconButton
        aria-label="close"
        onClick={onClose}
        style={{ position: 'absolute', right: 8, top: 8 }}
        size="small"
      >
        <CloseIcon />
      </IconButton>

      <DialogContent dividers>
        {isDelete && (
          <div style={{ marginBottom: 12 }}>
            Are you sure you want to delete this Web Client Directory entry? This action cannot be undone.
          </div>
        )}

        <Field label="User Id" value={data.userId} />
        <Field label="Client Id" value={data.clientId} />
        <Field label="x500 Id" value={data.pathTx} />
        <Field label="Report ID" value={String(data.webReportId ?? '').padStart(5, '0')} />
      </DialogContent>

      <DialogActions>
        <div style={{ display: 'flex', gap: 8, padding: 8 }}>
          <Button variant="outlined" size="small" onClick={onClose}>
            {isDelete ? 'Cancel' : 'Close'}
          </Button>
          {isDelete && (
            <Button
              variant="contained"
              size="small"
              color="error"
              onClick={() => { onConfirmDelete?.(); onClose?.(); }}
            >
              Confirm
            </Button>
          )}
        </div>
      </DialogActions>
    </Dialog>
  );
};

WebClientDirectoryDialog.propTypes = {
  open: PropTypes.bool.isRequired,
  onClose: PropTypes.func.isRequired,
  mode: PropTypes.oneOf(['review', 'delete']),
  title: PropTypes.string,
  initialData: PropTypes.shape({
    userId: PropTypes.string,
    clientId: PropTypes.string,
    pathTx: PropTypes.string,
    webReportId: PropTypes.oneOfType([PropTypes.number, PropTypes.string]),
  }),
  onConfirmDelete: PropTypes.func,
};

export default WebClientDirectoryDialog;





