





i have this file "import React, { useState, useMemo, useRef, useEffect } from 'react';
import { Dialog, DialogTitle, DialogContent, DialogActions, Button } from '@mui/material';
import IconButton from '@mui/material/IconButton';
import { CloseIcon, EditIcon, ClientMappingDeleteIcon, SearchIcon, ApplicationEventId } from '../../../assets/brand/svg-constants';
import { CFormTextarea, CFormInput, CFormCheck, CFormSelect } from '@coreui/react';
import * as emailEventIdService from '../../../services/AdminReportService/EmailEventIdService';
import { AgGridReact } from "ag-grid-react";
import { ModuleRegistry, AllCommunityModule } from "ag-grid-community";
import CustomSnackbar from '../../../components/CustomSnackbar';
import '../../../scss/EmailEventId.scss';

ModuleRegistry.registerModules([
    AllCommunityModule,
]);

const EmailEventId = ({ open, onClose }) => {
    const containerStyle = useMemo(() => ({ width: "100%", height: "100%" }), []);
    const gridStyle = useMemo(() => ({ height: "100%", width: "100%" }), []);

    const [columnDefs, setColumnDefs] = useState([
        {
            field: 'eventId',
            headerName: 'Event ID',
            maxWidth: 100,
        },
        { field: 'applicationId', headerName: 'Application', maxWidth: 188 },
        { field: 'eventTxt', headerName: 'Description', minWidth: 143 },
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
    const rowSelection = useMemo(() => ({ mode: 'multiRow' }), []);
    // State to track selected rows
    const [selectedRows, setSelectedRows] = useState([]);
    const [deleteSnackbarOpen, setDeleteSnackbarOpen] = useState(false);
    // Snackbar state: 'none', 'add', 'update', 'delete', 'delete-confirmation'
    const [snackbarType, setSnackbarType] = useState('none');

    const [selectedRowToDelete, setSelectedRowToDelete] = useState(null);
    const [data, setData] = useState({
        eventId: '',
        applicationId: '',
        eventTxt: '',
    });
    const [originalData, setOriginalData] = useState(null);
    const [enableAddContent, setEnableAddContent] = useState(false);
    const [pageSize, setPageSize] = useState(10);
    const gridApi = useRef(null);

    const [applicationDropdownOptions, setApplicationDropdownOptions] = useState([]);
    const [eventIdDropdownOptions, setEventIdDropdownOptions] = useState([]);

    useEffect(() => {
        // Fetch initial data when the dialog opens
        if (open) {
            getApplicationIdList();
            getEventIdList();
        }
    }, [open]);



    /**
    * Clears the form data and resets the originalData state.
    * Used when starting a new add or after a successful save.
    */
    const onHandleClear = () => {
        setOriginalData(null);
        setData({
            eventId: '',
            applicationId: '',
            eventTxt: '',
        });
    };


    // Store all fetched data for client-side filtering
    const [allRows, setAllRows] = useState([]);


    const getApplicationIdList = async () => {
        try {
            const response = await emailEventIdService.fetchApplicationIdList();
            setApplicationDropdownOptions(response.response.ApplicationIdList);
            console.log('Application ID List:', applicationDropdownOptions);
        } catch (error) {
            console.error('Error fetching application ID list:', error);
        }

    };

    const getEventIdList = async () => {
        try {
            const response = await emailEventIdService.fetchEventIdList();
            setEventIdDropdownOptions(response.response.EventIdList);
            console.log('Event ID List:', eventIdDropdownOptions);
        } catch (error) {
            console.error('Error fetching event ID list:', error);
        }
    };



    /*
    * Creates a data source object for AG Grid's infinite row model.
    * Fetches paginated data from the backend using the provided page size.
    * @param {number} pageSizeArg - The number of rows per page.
     * @returns {object} AG Grid data source with getRows callback.
     */

    const createDataSource = (pageSizeArg) => ({
        getRows: (params) => {
            const page = params.startRow / pageSizeArg;
            emailEventIdService.fetchEmailEventIds(page, pageSizeArg)
                .then(data => {
                    const response = data.response.EventIdList;
                    let rows, lastRow;
                    if (response && Array.isArray(response.rows) && typeof response.totalElements === 'number') {
                        rows = response.rows;
                        lastRow = response.totalElements;
                    } else if (Array.isArray(response) && typeof response.totalElements === 'number') {
                        rows = response;
                        lastRow = response.totalElements;
                    } else if (Array.isArray(response)) {
                        rows = response;
                        lastRow = rows.length < pageSizeArg ? params.startRow + rows.length : -1;
                    } else {
                        rows = [];
                        lastRow = 0;
                    }
                    setAllRows(rows);
                    console.log('AG Grid getRows callback', { page, pageSizeArg, rows, lastRow, response });
                    params.successCallback(rows, lastRow);
                })
                .catch(() => {
                    params.failCallback();
                });
        }
    });

    /**
     * Handler for the Add button click.
     * Clears the form and enables the add content UI.
     */
    const handleAddClick = () => {
        onHandleClear();
        setEnableAddContent(true);
    }

    /**
     * Handles the deletion of a selected row.
     * Calls the delete service and refreshes the grid on success.
     */
    const onHandleDelete = async () => {
        setDeleteSnackbarOpen(false);
        if (selectedRowToDelete.eventId) {
            try {
                await emailEventIdService.deleteEmailEventId(selectedRowToDelete.eventId, selectedRowToDelete.applicationId);
                onHandleClear();
                setSnackbarType('delete-confirmation');
                if (gridApi.current) {
                    gridApi.current.refreshInfiniteCache();
                }
            } catch (err) {
            }
        }
    }

    /**
     * Handler for the Edit icon click in the grid.
     * Loads the selected row's data into the form for editing.
     * @param {object} rowData - The row data to edit.
     */
    const handleEditClick = (rowData) => {
        setEnableAddContent(true);
        setData(rowData);
        setOriginalData(rowData);
    };

    /**
     * Handler for the Delete icon click in the grid.
     * Opens the delete confirmation snackbar.
     * @param {object} rowData - The row data to delete.
     */
    const handleDeleteClick = (rowData) => {
        setSelectedRowToDelete(rowData);
        setDeleteSnackbarOpen(true);
    };

    /**
     * Handles the Save button click.
     * Determines whether to add or update based on originalData.
     */
    const handleSave = async () => {
        if (originalData) {
            await onUpdate();
        } else {
            await onAdd();
        }
    }

    /**
     * Updates an existing email event ID mapping.
     * updates and refreshes grid.
     */
    const onUpdate = async () => {
        try {
            const reqData = {
                applicationId: data.applicationId.trim(),
                eventId: data.eventId.trim(),
                eventTxt: data.eventTxt.trim()
            };
            await emailEventIdService.updateEmailEventId(reqData);
            setSnackbarType('update');
            setTimeout(() => onHandleClear(), 500);
            if (gridApi.current) {
                gridApi.current.refreshInfiniteCache();
            }
        } catch (err) {
        }
    }

    /**
     * Adds a new email event ID mapping.
     * adds and clears form.
     */
    const onAdd = async () => {
        try {
            const reqData = {
                applicationId: data.applicationId.trim(),
                eventId: data.eventId.trim(),
                eventTxt: data.eventTxt.trim()
            };
            await emailEventIdService.initiateEmailEventId(reqData);
            setSnackbarType('add');
            setTimeout(() => onHandleClear(), 500);
        } catch (err) {
        }
    }

    /**
     * Checks if the form data has changed compared to the original data.
     * Used to enable/disable the Save button.
     * @returns {boolean} True if data has changed, false otherwise.
     */
    const isDataChanged = () => {
        if (!originalData) {
            return (
                (
                    data.applicationId.trim() !== '' &&
                    data.eventTxt.trim() !== '' && data.eventId.trim() !== '')
            );
        }

        return JSON.stringify(data) !== JSON.stringify(originalData);
    };

    /**
     * Handles closing the dialog.
     * Prevents close on backdrop click or escape key.
     * Resets add content state and calls parent onClose.
     */
    const handleClose = (event, reason) => {
        if (reason === 'backdropClick' || reason === 'escapeKeyDown') {
            return;
        }
        setEnableAddContent(false);
        onClose();
    };

    /**
    * Cancels and closes any open snackbar.
    * Resets snackbar and add content state.
    */
    const handleSnackbarCancel = () => {
        setDeleteSnackbarOpen(false);
        setEnableAddContent(false);
        setSnackbarType('none');
    };

    /**
     * AG Grid's onGridReady event handler.
     * Stores the grid API reference for later use.
     * @param {object} params - AG Grid params containing the API.
     */
    const onGridReady = (params) => {
        gridApi.current = params.api;
    };

    // Re-set datasource when pageSize changes (for infinite row model)
    const dataSource = useMemo(() => createDataSource(pageSize), [pageSize]);

    return (
        <>
            <Dialog open={open} onClose={handleClose} PaperProps={{ className: 'email-event-id-dialog' }}>
                <DialogTitle><div className='application-event-id-icon'><ApplicationEventId /></div>Trace Admin Application Event Id</DialogTitle>
                <IconButton aria-label="close" onClick={onClose}>
                    <CloseIcon />
                </IconButton>
                {enableAddContent ? null : (<DialogActions>
                    <div className='action-container'>
                        {selectedRows.length > 1 ? (
                            <div style={{ display: 'flex', alignItems: 'center', gap: 8 }}>
                                <span className='text-count'>{selectedRows.length} rows selected</span>
                                <Button
                                    variant="outlined"
                                    size="small"
                                    onClick={() => {
                                        console.log('Selected rows for delete:', selectedRows);
                                    }}
                                >
                                    Delete
                                </Button>
                            </div>
                        ) : (
                            <>
                                <div className='search-input'>
                                    <CFormInput
                                        placeholder='Search'
                                    />
                                    <span className='search-icon'>
                                        <SearchIcon />
                                    </span>
                                </div>
                            </>
                        )}
                        <div>
                            <Button variant="contained" size="small" onClick={handleAddClick}>Add</Button>
                        </div>
                    </div>
                </DialogActions>)}
                <DialogContent>
                    {enableAddContent ? (
                        <div className='add-main-container'>
                            <div className='row-container'>
                                <div className='eventId-container eventId-field'>
                                    <span className='label' data-testid='client-id-label'>Event ID</span>
                                    <CFormSelect className='eventId-input applicationId-input'
                                        value={data.eventId}
                                        onChange={(e) => setData({ ...data, eventId: e.target.value })}
                                    >
                                        <option value=''></option>
                                        {eventIdDropdownOptions.map((option) => (
                                            <option key={option} value={option}>
                                                {option}
                                            </option>
                                        ))}
                                    </CFormSelect>
                                </div>
                                <div className='eventId-container eventId-field'>
                                    <span className='label' data-testid="report-client-id-label">Application</span>
                                    <CFormSelect className='eventId-input applicationId-input'
                                        value={data.applicationId}
                                        onChange={(e) => setData({ ...data, applicationId: e.target.value })}
                                    >
                                        <option value=''></option>
                                        {applicationDropdownOptions.map((option) => (
                                            <option key={option} value={option}>
                                                {option}
                                            </option>
                                        ))}
                                    </CFormSelect>
                                </div>

                            </div>
                            <div className='row-container'>
                                <div className='description-container eventId-field'>
                                    <span className='label'>Description</span>
                                    <CFormTextarea
                                        className='eventId-input description-input'
                                        value={data.eventTxt}
                                        onChange={(e) => setData({ ...data, eventTxt: e.target.value })}
                                    />
                                </div>
                            </div>
                        </div>
                    ) : (
                        <div style={containerStyle}>
                            <div style={gridStyle}>
                                <AgGridReact
                                    columnDefs={columnDefs}
                                    rowModelType='infinite'
                                    defaultColDef={defaultColDef}
                                    rowSelection={rowSelection}
                                    pagination={true}
                                    paginationPageSize={pageSize}
                                    paginationPageSizeSelector={[10, 20, 50, 100]}
                                    cacheBlockSize={pageSize}
                                    infiniteInitialRowCount={pageSize}
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
                    )
                    }
                </DialogContent >
                {enableAddContent ? (<DialogActions className='add-action-container'>
                    <div className='email-event-id-button-container'>
                        <Button variant="outlined" size="small" onClick={handleClose}>Cancel</Button>
                        <Button variant="contained" size="small" disabled={!isDataChanged()} onClick={handleSave}>Save</Button>
                    </div>
                </DialogActions>) : null}
            </Dialog >
            <CustomSnackbar type="delete" open={deleteSnackbarOpen} onClose={handleSnackbarCancel} handleOk={onHandleDelete} title="Confirm Delete" body="Are you sure you want to delete?" />
            <CustomSnackbar
                type={snackbarType}
                open={snackbarType !== 'none'}
                handleOk={handleSnackbarCancel}
                onClose={handleSnackbarCancel}
                title={
                    snackbarType === 'add' ? 'Client Report Mapping' :
                        snackbarType === 'update' ? 'Client Report Mapping' :
                            snackbarType === 'delete-confirmation' ? 'Client Report Mapping' : ''
                }
                body={
                    snackbarType === 'add' ? 'You have successfully Added a new client' :
                        snackbarType === 'update' ? 'You have successfully Updated a client' :
                            snackbarType === 'delete-confirmation' ? 'You have successfully Deleted a client' : ''
                }
            />
        </>
    );
};

export default EmailEventId;", when I click "Add" button and edit icon, i want open a dialog window which is like "import React, { useEffect, useState } from 'react';
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



export default MailTypeDialog;". can you create this EmailEventIdDialog window and update EmailEventId file for me ?




















import React, { useEffect, useState } from 'react';
import { Dialog, DialogTitle, DialogContent, DialogActions, Button, IconButton } from '@mui/material';
import { CloseIcon } from '../../../assets/brand/svg-constants';
import { CFormInput, CFormSelect, CFormTextarea } from '@coreui/react';
import * as emailEventIdService from '../../../services/AdminReportService/EmailEventIdService';
import CustomSnackbar from '../../../components/CustomSnackbar';
import '../../../scss/EmailEventId.scss';

/**
 * Child dialog used for Add/Edit of Event-Id mapping.
 * If `selectedRow` is null -> Add mode, otherwise Edit mode.
 */
const EmailEventIdDialog = ({
  open,
  onClose,
  selectedRow,
  applicationOptions = [],
  eventIdOptions = [],
  onSuccess,
}) => {
  const [form, setForm] = useState({
    eventId: '',
    applicationId: '',
    eventTxt: '',
  });
  const [original, setOriginal] = useState(null);
  const [snackbarType, setSnackbarType] = useState('none');

  // initialize on open
  useEffect(() => {
    if (!open) return;
    if (selectedRow) {
      // edit
      setForm({
        eventId: String(selectedRow.eventId ?? ''),
        applicationId: String(selectedRow.applicationId ?? ''),
        eventTxt: String(selectedRow.eventTxt ?? ''),
      });
      setOriginal({
        eventId: String(selectedRow.eventId ?? ''),
        applicationId: String(selectedRow.applicationId ?? ''),
        eventTxt: String(selectedRow.eventTxt ?? ''),
      });
    } else {
      // add
      setForm({ eventId: '', applicationId: '', eventTxt: '' });
      setOriginal(null);
    }
  }, [open, selectedRow]);

  const handleClose = (event, reason) => {
    if (reason === 'backdropClick' || reason === 'escapeKeyDown') return;
    onClose?.();
  };

  const changed = () => {
    if (!original) {
      // Add mode: require required fields
      return form.eventId.trim() !== '' && form.applicationId.trim() !== '' && form.eventTxt.trim() !== '';
    }
    return JSON.stringify(form) !== JSON.stringify(original);
  };

  const handleSave = async () => {
    try {
      const payload = {
        eventId: form.eventId.trim(),
        applicationId: form.applicationId.trim(),
        eventTxt: form.eventTxt.trim(),
      };

      if (selectedRow) {
        // update
        await emailEventIdService.updateEmailEventId(payload);
        onSuccess?.('update');
      } else {
        // add
        await emailEventIdService.initiateEmailEventId(payload);
        onSuccess?.('add');
      }
    } catch (err) {
      // You can show an error snackbar if you want
      setSnackbarType('info');
    }
  };

  return (
    <Dialog
      open={open}
      onClose={handleClose}
      PaperProps={{ className: 'email-event-id-edit-dialog' }} // unique class
      keepMounted
      slotProps={{
        backdrop: { style: { zIndex: 1400 } },
        paper: { style: { zIndex: 1450 } },
      }}
    >
      <DialogTitle>{selectedRow ? 'Edit Event Mapping' : 'Add Event Mapping'}</DialogTitle>
      <IconButton aria-label="close" onClick={handleClose}>
        <CloseIcon />
      </IconButton>

      <DialogContent dividers>
        <div className="email-event-id-edit-form">
          <div className="row">
            <div className="field">
              <span className="label">Event ID</span>
              <CFormSelect
                value={form.eventId}
                onChange={(e) => setForm((p) => ({ ...p, eventId: e.target.value }))}
                aria-label="Event Id"
                disabled={!!selectedRow} // lock when editing
              >
                <option value=""></option>
                {eventIdOptions.map((opt) => (
                  <option key={opt} value={opt}>
                    {opt}
                  </option>
                ))}
              </CFormSelect>
            </div>

            <div className="field">
              <span className="label">Application</span>
              <CFormSelect
                value={form.applicationId}
                onChange={(e) => setForm((p) => ({ ...p, applicationId: e.target.value }))}
                aria-label="Application"
                disabled={!!selectedRow} // lock when editing
              >
                <option value=""></option>
                {applicationOptions.map((opt) => (
                  <option key={opt} value={opt}>
                    {opt}
                  </option>
                ))}
              </CFormSelect>
            </div>
          </div>

          <div className="row">
            <div className="field field-wide">
              <span className="label">Description</span>
              <CFormTextarea
                value={form.eventTxt}
                onChange={(e) => setForm((p) => ({ ...p, eventTxt: e.target.value }))}
                rows={3}
              />
            </div>
          </div>
        </div>
      </DialogContent>

      <DialogActions>
        <div className="email-event-id-edit-buttons">
          <Button variant="outlined" size="small" onClick={handleClose}>
            Cancel
          </Button>
          <Button variant="contained" size="small" onClick={handleSave} disabled={!changed()}>
            Save
          </Button>
        </div>
      </DialogActions>

      <CustomSnackbar
        type={snackbarType}
        open={snackbarType !== 'none'}
        onClose={() => setSnackbarType('none')}
        handleOk={() => setSnackbarType('none')}
        title={snackbarType === 'info' ? 'Action Failed' : ''}
        body={snackbarType === 'info' ? 'Something went wrong. Please try again.' : ''}
      />
    </Dialog>
  );
};

export default EmailEventIdDialog;




/* Parent dialog (existing) */
.email-event-id-dialog {
  /* your existing styles */
}

/* Child dialog (ensure it's above the parent modal) */
.email-event-id-edit-dialog {
  z-index: 1450; /* paper z-index */
}

.email-event-id-edit-form {
  display: grid;
  gap: 12px;
}

.email-event-id-edit-form .row {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 12px;
}

.email-event-id-edit-form .field {
  display: flex;
  flex-direction: column;
}

.email-event-id-edit-form .field-wide {
  grid-column: 1 / -1;
}

.email-event-id-edit-buttons {
  display: flex;
  gap: 8px;
  align-items: center;
}




