import React, { useState, useMemo, useRef, useEffect } from 'react';
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

const EmailEventIdDialog = ({ open, onClose }) => {
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

export default EmailEventIdDialog;
