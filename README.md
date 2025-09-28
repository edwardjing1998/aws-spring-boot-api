import React, { useState, useRef, useMemo, useEffect } from 'react';
import CustomSnackbar from '../../../components/CustomSnackbar';
import { EditIcon, ClientMappingDeleteIcon, SearchIcon } from '../../../assets/brand/svg-constants';
import TitleContainer from '../../../components/TittleContainer';
import * as mailTypeService from '../../../services/AdminEditService/MailTypeService';
import { AgGridReact } from 'ag-grid-react';
import {
  ModuleRegistry,
  AllCommunityModule,
  RowSelectionModule,
  ValidationModule,
} from 'ag-grid-community';
import 'ag-grid-community/styles/ag-theme-quartz.css';
import '../../../scss/MailType.scss';

ModuleRegistry.registerModules([
  AllCommunityModule,
  RowSelectionModule,
  ValidationModule,
]);

const MailType = () => {
  // Layout
  const containerStyle = useMemo(() => ({ width: '100%', height: '500px', paddingTop: '16px' }), []);
  const gridStyle = useMemo(() => ({ height: '100%', width: '100%' }), []);
  const defaultColDef = useMemo(() => ({ flex: 1, minWidth: 100 }), []);
  const rowSelection = useMemo(() => ({ mode: 'multiRow' }), []);
  const gridApi = useRef(null);

  // Table / paging
  const [pageSize, setPageSize] = useState(10);
  const [refreshKey, setRefreshKey] = useState(0);
  const [loading, setLoading] = useState(true);

  // Data
  const [allRows, setAllRows] = useState([]);

  // Selection
  const [selectedRows, setSelectedRows] = useState([]);
  const [selectedEditRows, setSelectedEditRows] = useState({}); // for dialog (add/edit/delete single row)

  // Dialog
  const [isDialogOpen, setIsDialogOpen] = useState(false);
  const [dialogMode, setDialogMode] = useState('addEdit'); // 'addEdit' | 'delete'

  // Snackbars
  // 'none' | 'add' | 'update' | 'delete' (confirm UI) | 'delete-confirmation'
  const [snackbarType, setSnackbarType] = useState('none');

  // Columns
  const [columnDefs] = useState([
    { field: 'name', headerName: 'Mail Type' },
    { field: 'postageCategory', headerName: 'Category' },
    { field: 'weight', headerName: 'Weight' },
    { field: 'active', headerName: 'Status', valueFormatter: (p) => (p.value === 1 ? 'active' : 'inactive') },
    {
      headerName: '',
      minWidth: 127,
      field: 'actions',
      cellClass: 'actions-cell-flex-end',
      cellRenderer: (params) => (
        <div className="actions-cell icon-container action-cell-flex">
          <span className="icon-wrapper">
            <EditIcon
              style={{ cursor: 'pointer' }}
              onClick={() => params.context.handleEditClick(params.data)}
            />
          </span>
          <span className="icon-wrapper">
            <ClientMappingDeleteIcon
              data-testid="delete-icon"
              style={{ cursor: 'pointer' }}
              onClick={() => params.context.handleDeleteClick(params.data)}
            />
          </span>
        </div>
      ),
    },
  ]);

  // Loading overlay toggle
  useEffect(() => {
    if (!gridApi.current) return;
    if (loading) gridApi.current.showLoadingOverlay();
    else gridApi.current.hideOverlay();
  }, [loading]);

  // Handlers to open dialog
  const showAddDialog = () => {
    setSelectedEditRows({});
    setDialogMode('addEdit');
    setIsDialogOpen(true);
  };

  const handleEditClick = (rowData) => {
    setSelectedEditRows(rowData);
    setDialogMode('addEdit');
    setIsDialogOpen(true);
  };

  // Row delete -> open dialog in delete mode
  const handleDeleteClick = (rowData) => {
    setSelectedEditRows(rowData);
    setDialogMode('delete');
    setIsDialogOpen(true);
  };

  // Toolbar delete keeps your existing snackbar confirmation flow (bulk)
  const handleToolbarDelete = () => {
    if (!selectedRows.length) return;
    setSnackbarType('delete');
  };

  const handleSnackbarCancel = () => {
    setSelectedEditRows({});
    setSelectedRows([]);
    setSnackbarType('none');
  };

  const handleSnackbarOk = async () => {
    setSnackbarType('none');
    const selectedDeletedMailTypecds = selectedRows.map((r) => r.mailTypeCd).filter(Boolean);
    const payLoad = { mailTypeCd: selectedDeletedMailTypecds };
    try {
      await mailTypeService.deleteMailType(payLoad);
      setSnackbarType('delete-confirmation');
      setRefreshKey((prev) => prev + 1);
    } catch {}
  };

  // Dialog success (add/update/delete-confirmation)
  const handleDialogSuccess = async (type) => {
    setIsDialogOpen(false);
    setSnackbarType(type);
    setRefreshKey((prev) => prev + 1);
  };

  // AG Grid datasource (infinite model)
  const createDataSource = (pageSizeArg) => ({
    getRows: (params) => {
      setLoading(true);
      const page = params.startRow / pageSizeArg;
      mailTypeService
        .fetchMailAllPackageTypesDetails(page, pageSizeArg)
        .then((data) => {
          const mailTypeData = data.response.deliveryTypeList;
          let rows, lastRow;
          if (mailTypeData && Array.isArray(mailTypeData.rows) && typeof mailTypeData.totalElements === 'number') {
            rows = mailTypeData.rows;
            lastRow = mailTypeData.totalElements;
          } else if (Array.isArray(mailTypeData) && typeof mailTypeData.totalElements === 'number') {
            rows = mailTypeData;
            lastRow = mailTypeData.totalElements;
          } else if (Array.isArray(mailTypeData)) {
            rows = mailTypeData;
            lastRow = rows.length < pageSizeArg ? params.startRow + rows.length : -1;
          } else {
            rows = [];
            lastRow = 0;
          }
          setAllRows(rows);
          setLoading(false);
          params.successCallback(rows, lastRow);
        })
        .catch(() => {
          setLoading(false);
          params.failCallback();
        });
    },
  });

  const dataSource = useMemo(() => createDataSource(pageSize), [pageSize]);

  const onGridReady = (params) => {
    gridApi.current = params.api;
  };

  return (
    <div>
      <TitleContainer
        onAdd={showAddDialog}
        onDelete={handleToolbarDelete}
        hideSaveButton={true}
        hideDeleteButton={selectedRows.length > 1 ? true : false}
        hideUpdateButton={false}
        selectedDeletedRows={selectedRows}
      />

      <div style={containerStyle} className="ag-theme-quartz">
        <div style={gridStyle}>
          <AgGridReact
            key={pageSize + '_' + refreshKey}
            columnDefs={columnDefs}
            rowModelType="infinite"
            defaultColDef={defaultColDef}
            rowSelection={rowSelection}
            pagination={true}
            paginationPageSize={pageSize}
            paginationPageSizeSelector={[10, 20, 50, 100]}
            cacheBlockSize={pageSize}
            cacheOverflowSize={1}
            maxConcurrentDatasourceRequests={1}
            context={{ handleEditClick, handleDeleteClick }}
            maxBlocksInCache={2}
            onGridReady={onGridReady}
            datasource={dataSource}
            onSelectionChanged={(params) => {
              setSelectedRows(params.api.getSelectedRows());
            }}
            onPaginationChanged={(params) => {
              const newPageSize = params.api.paginationGetPageSize();
              setPageSize((prev) => (prev !== newPageSize ? newPageSize : prev));
            }}
          />
        </div>
      </div>

      <MailTypeDialog
        open={isDialogOpen}
        onClose={() => setIsDialogOpen(false)}
        onSuccess={handleDialogSuccess}
        selectedRows={selectedEditRows}
        mode={dialogMode} // 'addEdit' | 'delete'
      />

      {/* Bulk delete confirm */}
      <CustomSnackbar
        open={snackbarType === 'delete'}
        onClose={handleSnackbarCancel}
        handleOk={handleSnackbarOk}
        title="Confirm Delete"
        body="Are you sure you want to delete?"
        type="delete"
      />

      {/* Status snackbars */}
      {(snackbarType === 'add' || snackbarType === 'update' || snackbarType === 'delete-confirmation') && (
        <CustomSnackbar
          type={snackbarType}
          open={snackbarType !== 'none'}
          handleOk={handleSnackbarCancel}
          onClose={handleSnackbarCancel}
          title="Mail Type"
          body={
            snackbarType === 'add'
              ? 'You have successfully Added a new mail type'
              : snackbarType === 'update'
              ? 'You have successfully Updated a mail type'
              : snackbarType === 'delete-confirmation'
              ? 'You have successfully Deleted a mail type'
              : ''
          }
        />
      )}
    </div>
  );
};

export default MailType;




import React, { useEffect, useState } from 'react';
import { Dialog, DialogTitle, DialogContent, DialogActions, Button, IconButton } from '@mui/material';
import { CloseIcon } from '../../../assets/brand/svg-constants';
import * as mailTypeService from '../../../services/AdminEditService/MailTypeService';
import { CFormInput, CFormSelect, CFormCheck } from '@coreui/react';
import CustomSnackbar from '../../../components/CustomSnackbar';

const MailTypeDialog = ({ open, onClose, selectedRows, onSuccess, mode = 'addEdit' }) => {
  const isDelete = mode === 'delete';

  const [mailTypeDetails, setMailTypeDetails] = useState({
    active: false,
    deliveryId: '',
    postageCategoryCd: '',
    weight: '',
  });
  const [mailTypeDropdownDetails, setMailTypeDropdownDetails] = useState([]);
  const [categoryDropdownDetails, setCategoryDropdownDetails] = useState([]);
  const [originalMailTypeDetails, setOriginalMailTypeDetails] = useState({});
  const [snackbarType, setSnackbarType] = useState('none');

  useEffect(() => {
    if (!open) return;

    getMailTypeDropdownDetails();
    getCategoryDropdownDetails();

    if (selectedRows && Object.keys(selectedRows).length > 0) {
      setMailTypeDetails({ ...selectedRows });
      setOriginalMailTypeDetails({ ...selectedRows });
    } else {
      setMailTypeDetails({
        active: false,
        deliveryId: '',
        postageCategoryCd: '',
        weight: '',
      });
      setOriginalMailTypeDetails({});
    }
  }, [open]); // eslint-disable-line react-hooks/exhaustive-deps

  const getMailTypeDropdownDetails = async () => {
    const response = await mailTypeService.fetchMasterCodes(0, 10, 'D');
    setMailTypeDropdownDetails(response.response.masterCodes);
  };

  const getCategoryDropdownDetails = async () => {
    const response = await mailTypeService.fetchMasterCodes(0, 10, 'R');
    setCategoryDropdownDetails(response.response.masterCodes);
  };

  const handleClose = (event, reason) => {
    if (reason === 'backdropClick' || reason === 'escapeKeyDown') return;
    onClose?.();
  };

  const getMailTypeIdByDeliveryId = async (deliveryId) => {
    try {
      const responseData = await mailTypeService.fetchMailTypeDetails(0, 10, deliveryId);
      return responseData.response?.mailTypes[0]?.mailTypeCd;
    } catch {
      return null;
    }
  };

  const handleMailTypeSave = async () => {
    const { postageCategoryCd, weight } = mailTypeDetails;

    // Require BOTH empty or BOTH filled
    const isPostageFilled = postageCategoryCd && postageCategoryCd.toString().trim() !== '';
    const isWeightFilled = weight && weight.toString().trim() !== '';
    if ((isPostageFilled && !isWeightFilled) || (!isPostageFilled && isWeightFilled)) {
      setSnackbarType('info');
      return;
    }

    let payload = {
      active: mailTypeDetails.active ? 1 : 0,
      deliveryId: Number(mailTypeDetails.deliveryId),
      mailTypeCd: selectedRows ? selectedRows.mailTypeCd : 0,
      postageCategoryCd: Number(mailTypeDetails.postageCategoryCd),
      weight: Number(mailTypeDetails.weight),
    };

    if (selectedRows && Object.keys(selectedRows).length > 0) {
      try {
        await mailTypeService.updateMailType(payload);
        onSuccess?.('update');
      } catch (error) {
        console.error(error);
      }
    } else {
      try {
        const mailTypeId = await getMailTypeIdByDeliveryId(Number(mailTypeDetails.deliveryId));
        if (mailTypeId) {
          payload.mailTypeCd = mailTypeId;
          await mailTypeService.addMailType(payload);
          onSuccess?.('add');
        }
      } catch (error) {
        console.error(error);
      }
    }
  };

  const handleDeleteConfirm = async () => {
    try {
      const payload = { mailTypeCd: [selectedRows?.mailTypeCd].filter(Boolean) };
      if (!payload.mailTypeCd.length) return;
      await mailTypeService.deleteMailType(payload);
      onSuccess?.('delete-confirmation');
    } catch (e) {
      console.error(e);
    }
  };

  const normalize = (obj) => ({
    active: Boolean(obj.active),
    deliveryId: String(obj.deliveryId ?? ''),
    postageCategoryCd: String(obj.postageCategoryCd ?? ''),
    weight: String(obj.weight ?? ''),
  });

  const isEqual = (a, b) => JSON.stringify(normalize(a)) === JSON.stringify(normalize(b));

  const isSaveDisabled = () => {
    if (isDelete) return false; // Not used in delete mode
    if (selectedRows && Object.keys(selectedRows).length > 0) {
      return isEqual(mailTypeDetails, originalMailTypeDetails);
    }
    return mailTypeDetails.deliveryId.toString().trim() === '';
  };

  return (
    <>
      <Dialog open={open} onClose={handleClose} PaperProps={{ className: 'mail-type-dialog' }}>
        <DialogTitle>{isDelete ? 'Delete Mail Type' : 'Mail Type'}</DialogTitle>
        <IconButton aria-label="close" onClick={handleClose}>
          <CloseIcon />
        </IconButton>

        <DialogContent dividers>
          {isDelete && (
            <div style={{ marginBottom: 12 }}>
              Are you sure you want to delete this mail type? This action cannot be undone.
            </div>
          )}

          <div className="mail-type-dialog-content">
            <div className="mail-type-dialog-content-details">
              <div className="mail-type-input-details-container">
                <span className="input-text">Mail Type</span>
                <CFormSelect
                  name="mailtype"
                  className="mail-type-textbox"
                  value={mailTypeDetails.deliveryId ?? ''}
                  onChange={(e) => setMailTypeDetails({ ...mailTypeDetails, deliveryId: e.target.value })}
                  disabled={isDelete || (selectedRows && Object.keys(selectedRows).length > 0)}
                  aria-label="Mail Type"
                >
                  <option value=" "></option>
                  {mailTypeDropdownDetails.map((mailType) => (
                    <option key={mailType.idNo} value={mailType.idNo}>
                      {mailType.description}
                    </option>
                  ))}
                </CFormSelect>
              </div>

              <div className="mail-type-input-details-container">
                <span className="input-text">Category</span>
                <CFormSelect
                  name="category"
                  className="mail-type-textbox"
                  value={mailTypeDetails.postageCategoryCd ?? ''}
                  onChange={(e) => setMailTypeDetails({ ...mailTypeDetails, postageCategoryCd: e.target.value })}
                  disabled={
                    isDelete ||
                    (selectedRows && Object.keys(selectedRows).length > 0) ||
                    mailTypeDetails.deliveryId.toString().trim() === ''
                  }
                  aria-label="Category"
                >
                  <option value=" "></option>
                  {categoryDropdownDetails.map((category) => (
                    <option key={category.idNo} value={category.idNo}>
                      {category.description}
                    </option>
                  ))}
                </CFormSelect>
              </div>
            </div>

            <div className="mail-type-dialog-content-details">
              <div className="weight-input-details-container">
                <span className="input-text">Weight</span>
                <CFormInput
                  name="weight"
                  className="weight-textbox"
                  value={mailTypeDetails.weight ?? ''}
                  onChange={(e) => setMailTypeDetails({ ...mailTypeDetails, weight: e.target.value })}
                  disabled={
                    isDelete ||
                    (selectedRows && Object.keys(selectedRows).length > 0
                      ? !mailTypeDetails.postageCategoryCd
                      : mailTypeDetails.deliveryId.toString().trim() === '')
                  }
                  aria-label="Weight"
                />
              </div>

              <div className="active-container">
                <CFormCheck
                  className="active-checkbox"
                  checked={Boolean(mailTypeDetails.active)}
                  onChange={(e) => setMailTypeDetails({ ...mailTypeDetails, active: e.target.checked })}
                  disabled={isDelete || mailTypeDetails.deliveryId.toString().trim() === ''}
                />
                <span>Active</span>
              </div>
            </div>
          </div>
        </DialogContent>

        <DialogActions>
          <div className="mail-type-button-container">
            <Button variant="outlined" size="small" onClick={handleClose}>
              Cancel
            </Button>
            {isDelete ? (
              <Button variant="contained" size="small" color="error" onClick={handleDeleteConfirm}>
                Confirm
              </Button>
            ) : (
              <Button variant="contained" size="small" onClick={handleMailTypeSave} disabled={isSaveDisabled()}>
                Save
              </Button>
            )}
          </div>
        </DialogActions>
      </Dialog>

      <CustomSnackbar
        type={snackbarType}
        open={snackbarType !== 'none'}
        handleOk={() => setSnackbarType('none')}
        onClose={() => setSnackbarType('none')}
        title={snackbarType === 'info' ? 'Confirm Action' : ''}
        body={
          snackbarType === 'info'
            ? 'Either the Category and the Weight fields must both be empty, or they must both be set.'
            : ''
        }
      />
    </>
  );
};

export default MailTypeDialog;


