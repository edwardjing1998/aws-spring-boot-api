import React, { useState, useRef, useMemo, useEffect } from 'react';
import CustomSnackbar from '../../../components/CustomSnackbar';
import { CloseIcon, EditIcon, ClientMappingDeleteIcon, SearchIcon } from '../../../assets/brand/svg-constants';
import TitleContainer from '../../../components/TittleContainer';
import * as mailTypeService from '../../../services/AdminEditService/MailTypeService';
import MailTypeDialog from './MailTypeDialog';
import { AgGridReact } from "ag-grid-react";
import {
  ModuleRegistry, AllCommunityModule,
  RowSelectionModule,
  ValidationModule,
} from "ag-grid-community";
import "ag-grid-community/styles/ag-theme-quartz.css";
import '../../../scss/MailType.scss';

ModuleRegistry.registerModules([
  AllCommunityModule,
  RowSelectionModule,
  ValidationModule,
]);

const MailType = () => {
  const containerStyle = useMemo(() => ({ width: "100%", height: "500px", paddingTop: "16px" }), []);
  const gridStyle = useMemo(() => ({ height: "100%", width: "100%" }), []);
  const rowSelection = useMemo(() => ({ mode: 'multiRow' }), []);
  const [mailTypeTableDetails, setMailTypeTableDetails] = useState([]);
  const [selectedRows, setSelectedRows] = useState([]);
  const [isDialogOpen, setIsDialogOpen] = useState(false);
  const [snackbarType, setSnackbarType] = useState('none');
  const [selectedEditRows, setSelectedEditRows] = useState({});
  const [pageSize, setPageSize] = useState(10);
  // Store all fetched data for client-side filtering
  const [allRows, setAllRows] = useState([]);
  const gridApi = useRef(null);
  const [loading, setLoading] = useState(true);
  const [refreshKey, setRefreshKey] = useState(0);

  const [columnDefs, setColumnDefs] = useState([
    { field: 'name', headerName: 'Mail Type' },
    { field: 'postageCategory', headerName: 'Category' },
    { field: 'weight', headerName: 'Weight' },
    { field: 'active', headerName: 'Status', valueFormatter: params => (params.value === 1 ? 'active' : 'inactive'), },
    {
      headerName: '',
      minWidth: 127,
      field: 'actions',
      cellClass: 'actions-cell-flex-end',
      cellRenderer: (params) => (
        <div className="actions-cell icon-container action-cell-flex">
          <span className="icon-wrapper">
            <EditIcon style={{ cursor: 'pointer' }} onClick={() => params.context.handleEditClick(params.data)} />
          </span>
          <span className="icon-wrapper">
            <ClientMappingDeleteIcon data-testid="delete-icon" style={{ cursor: 'pointer' }} onClick={() => params.context.handleDeleteClick(params.data)} />
          </span>
        </div>
      ),
    },
  ]);

  const defaultColDef = useMemo(() => {
    return {
      flex: 1,
      minWidth: 100,
    };
  }, []);

  useEffect(() => {
    if (!loading) {
      setColumnDefs((prev) => {
        // Avoid duplicate "active" column
        if (prev.some(col => col.field === 'active')) return prev;
        const newCols = [...prev];
        newCols.splice(3, 0, {
          field: 'active',
          headerName: 'Status',
          valueFormatter: params => (params.value === 1 ? 'active' : 'inactive'),
        });
        return newCols;
      });
    }
    if (gridApi.current) {
      if (loading) {
        gridApi.current.showLoadingOverlay();
      } else {
        gridApi.current.hideOverlay();
      }
    }
  }, [loading]);


  const handleEditClick = (rowData) => {
    setSelectedEditRows(rowData);
    setIsDialogOpen(true);
  }

  const handleDeleteClick = async (rowData) => {
    setSelectedRows(prev => [...prev, rowData]);
    setSnackbarType('delete');
  }


  const showAddDialog = () => {
    setSelectedEditRows({});
    setIsDialogOpen(true);
    // const response = await mailTypeService.addMailType()
  }

  /**
   * Handles the Cancel/Close action for any snackbar.
   * Resets the snackbarType to 'none'.
   */
  const handleSnackbarCancel = () => {
    setSelectedEditRows({});
    setSelectedRows([]);
    setSnackbarType('none');
  };

  /**
     * Handles the OK action in the delete confirmation snackbar.
     * Calls the delete API, clears the form, and shows the success snackbar.
     */
  const handleSnackbarOk = async () => {
    setSnackbarType('none');
    const selectedDeletedMailTypecds = selectedRows
      .map(row => row.mailTypeCd)
      .filter(Boolean);
    const payLoad = {
      mailTypeCd: selectedDeletedMailTypecds
    }
    try {
      // Pass the array directly to the delete API
      await mailTypeService.deleteMailType(payLoad);
      setSnackbarType('delete-confirmation');
      setRefreshKey(prev => prev + 1); // Refresh grid to get latest details
    } catch (error) {
      // Handle error if needed
    }
  };

  // Add a handler to be called after add/update success
  const handleDialogSuccess = async (type) => {
    setIsDialogOpen(false);
    setSnackbarType(type);
    setRefreshKey(prev => prev + 1);

  };

  /**
     * Creates a datasource object for AG Grid's infinite row model.
     * Fetches data from the API and provides it to the grid.
     * Also stores the fetched rows in allRows state for client-side filtering.
     * @param {number} pageSizeArg - The number of rows per page.
     */
  const createDataSource = (pageSizeArg) => ({
    getRows: (params) => {
      setLoading(true);
      const page = params.startRow / pageSizeArg;
      mailTypeService.fetchMailAllPackageTypesDetails(page, pageSizeArg)
        .then(data => {
          const mailTypeData = data.response.deliveryTypeList
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
          console.log('AG Grid getRows callback', { page, pageSizeArg, rows, lastRow, mailTypeData });
          params.successCallback(rows, lastRow);
        })
        .catch(() => {
          setLoading(false);
          params.failCallback();
        });
    }
  });

  /**
     * Handler for AG Grid's onGridReady event.
     * Stores the grid API reference for later use (e.g., filtering, selection).
     * @param {object} params - AG Grid event params containing the API.
     */
  const onGridReady = (params) => {
    gridApi.current = params.api;
  };

  // Re-set datasource when pageSize changes (for infinite row model)
  const dataSource = useMemo(() => createDataSource(pageSize), [pageSize]);

  return (
    <div>
      <TitleContainer
//        title="Mail Type"
        onAdd={showAddDialog}
        onDelete={handleDeleteClick}
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
            rowModelType='infinite'
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
            onSelectionChanged={params => {
              setSelectedRows(params.api.getSelectedRows());
            }}
            onPaginationChanged={params => {
              const newPageSize = params.api.paginationGetPageSize();
              setPageSize(prev => (prev !== newPageSize ? newPageSize : prev));
            }}
          />
        </div>
      </div>
      <MailTypeDialog open={isDialogOpen} onClose={() => setIsDialogOpen(false)} onSuccess={handleDialogSuccess} selectedRows={selectedEditRows} />
      <CustomSnackbar
        open={snackbarType === 'delete'}
        onClose={handleSnackbarCancel}
        handleOk={handleSnackbarOk}
        title="Confirm Delete"
        body="Are you sure you want to delete?"
        type="delete"
      />
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
import Dialog from '@mui/material/Dialog';
import DialogTitle from '@mui/material/DialogTitle';
import DialogContent from '@mui/material/DialogContent';
import DialogActions from '@mui/material/DialogActions';
import Button from '@mui/material/Button';
import IconButton from '@mui/material/IconButton';
import { CloseIcon } from '../../../assets/brand/svg-constants';
import * as mailTypeService from '../../../services/AdminEditService/MailTypeService';
import { CFormInput, CFormSelect, CFormCheck } from '@coreui/react';
import CustomSnackbar from '../../../components/CustomSnackbar';

const MailTypeDialog = ({ open, onClose, selectedRows, onSuccess }) => {

    const [mailTypeDetails, setMailTypeDetails] = useState({
        active: false,
        deliveryId: '',
        postageCategoryCd: '',
        weight: ''
    });
    const [mailTypeDropdownDetails, setMailTypeDropdownDetails] = useState([]);
    const [categoryDropdownDetails, setCategoryDropdownDetails] = useState([]);
    const [originalMailTypeDetails, setOriginalMailTypeDetails] = useState({});
    const [mailType, setMailTypeId] = useState('')
    const [snackbarType, setSnackbarType] = useState('none');

    useEffect(() => {
        if (open) {
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
                    weight: ''
                });
            }

        }
    }, [open]);


    const getMailTypeDropdownDetails = async () => {
        const response = await mailTypeService.fetchMasterCodes(0, 10, 'D');
        setMailTypeDropdownDetails(response.response.masterCodes)
    }

    const getCategoryDropdownDetails = async () => {
        const response = await mailTypeService.fetchMasterCodes(0, 10, 'R');
        setCategoryDropdownDetails(response.response.masterCodes)
    }

    /**
   * Handles closing the dialog.
   * Prevents closing on backdrop click or escape key.
   * Clears the form and calls the parent onClose.
   * @param {object} event - The close event.
   * @param {string} reason - The reason for closing.
   */
    const handleClose = (event, reason) => {
        if (reason === 'backdropClick' || reason === 'escapeKeyDown') {
            return;
        }
        onClose();
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

        // If one is filled and the other is not, show an error and return
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
            weight: Number(mailTypeDetails.weight)

        };
        if (selectedRows && Object.keys(selectedRows).length > 0) {
            try {
                await mailTypeService.updateMailType(payload);
                onSuccess('update');
            } catch (error) {
                console.error(error);
            }
        } else {
            try {
                const mailTypeId = await getMailTypeIdByDeliveryId(Number(mailTypeDetails.deliveryId));
                if (mailTypeId) {
                    payload.mailTypeCd = mailTypeId;
                    await mailTypeService.addMailType(payload);
                    onSuccess('add');
                }
            } catch (error) {
                console.error(error);
            }
        }
    };

    /**
   * Handler for closing any open snackbar.
   * Resets all snackbar open states to false.
   */
    const handleSnackbarCancel = () => {
        setSnackbarType('none');
    };

    const normalize = obj => ({
        active: Boolean(obj.active),
        deliveryId: String(obj.deliveryId ?? ''),
        postageCategoryCd: String(obj.postageCategoryCd ?? ''),
        weight: String(obj.weight ?? '')
    });

    const isEqual = (a, b) => {
        const normA = normalize(a);
        const normB = normalize(b);
        return JSON.stringify(normA) === JSON.stringify(normB);
    };

    const isSaveDisabled = () => {
        if (selectedRows && Object.keys(selectedRows).length > 0) {
            // Disable if no changes
            return isEqual(mailTypeDetails, originalMailTypeDetails);
        } else {
            // Disable if required field is empty
            return mailTypeDetails.deliveryId.toString().trim() === '';
        }
    };

    return (
        <>
            <Dialog open={open} onClose={handleClose} PaperProps={{ className: 'mail-type-dialog' }}>
                <DialogTitle>Mail Type</DialogTitle>
                <IconButton
                    aria-label="close"
                    onClick={handleClose}
                >
                    <CloseIcon />
                </IconButton>
                <DialogContent dividers>
                    <div className='mail-type-dialog-content'>
                        <div className='mail-type-dialog-content-details'>
                            <div className='mail-type-input-details-container'>
                                <span className='input-text'>Mail Type</span>
                                <CFormSelect name="mailtype" className='mail-type-textbox' value={mailTypeDetails.deliveryId ?? ''}
                                    onChange={(e) => setMailTypeDetails({ ...mailTypeDetails, deliveryId: e.target.value })}
                                    disabled={selectedRows && Object.keys(selectedRows).length > 0} aria-label="Mail Type">
                                    <option value=" "></option>
                                    {mailTypeDropdownDetails.map((mailType) => (
                                        <option key={mailType.idNo} value={mailType.idNo}>
                                            {mailType.description}
                                        </option>
                                    ))}
                                </CFormSelect>
                            </div>
                            <div className='mail-type-input-details-container'>
                                <span className='input-text'>Category</span>
                                <CFormSelect name="category" className='mail-type-textbox' value={mailTypeDetails.postageCategoryCd ?? ''}
                                    onChange={(e) => setMailTypeDetails({ ...mailTypeDetails, postageCategoryCd: e.target.value })}
                                    disabled={selectedRows && Object.keys(selectedRows).length > 0 || mailTypeDetails.deliveryId.toString().trim() === ''} aria-label="Category">
                                    <option value=" "></option>
                                    {categoryDropdownDetails.map((category) => (
                                        <option key={category.idNo} value={category.idNo}>
                                            {category.description}
                                        </option>
                                    ))}
                                </CFormSelect>
                            </div>
                        </div>
                        <div className='mail-type-dialog-content-details'>
                            <div className='weight-input-details-container'>
                                <span className='input-text'>Weight</span>
                                <CFormInput name="weight" className='weight-textbox' value={mailTypeDetails.weight ?? ''}
                                    onChange={(e) => setMailTypeDetails({ ...mailTypeDetails, weight: e.target.value })}
                                    disabled={(selectedRows && Object.keys(selectedRows).length > 0 ? !mailTypeDetails.postageCategoryCd : mailTypeDetails.deliveryId.toString().trim() === '')} aria-label="Weight" />
                            </div>
                            <div className='active-container'>
                                <CFormCheck className="active-checkbox" checked={mailTypeDetails.active}
                                    onChange={(e) => setMailTypeDetails({ ...mailTypeDetails, active: e.target.checked })}
                                    disabled={mailTypeDetails.deliveryId.toString().trim() === ''} />
                                <span>Active</span>
                            </div>
                        </div>
                    </div>

                </DialogContent>
                <DialogActions>
                    <div className='mail-type-button-container'>
                        <Button variant="outlined" size="small" onClick={handleClose}>Cancel</Button>
                        <Button variant="contained" size="small" onClick={handleMailTypeSave} disabled={isSaveDisabled()}>Save</Button>
                    </div>
                </DialogActions>
            </Dialog>
            <CustomSnackbar
                type={snackbarType}
                open={snackbarType !== 'none'}
                handleOk={handleSnackbarCancel}
                onClose={handleSnackbarCancel}
                title={
                    snackbarType === 'info' ? 'Confirm Action' : ''
                }
                body={
                    snackbarType === 'info' ? 'Either the Category and the Weight fields must both be empty, or they must both be set.' : ''
                }
            />
        </>


    );
};



export default MailTypeDialog;
