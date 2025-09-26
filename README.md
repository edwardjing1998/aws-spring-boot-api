import React, { useRef, useState, useMemo } from "react";
import { Dialog, DialogTitle, DialogContent, DialogActions, Button } from '@mui/material';
import IconButton from '@mui/material/IconButton';
import { CFormInput} from '@coreui/react';
import * as InputRobotTotalsService from '../../../../services/AdminReportService/InputRobotTotalsService'
import { CloseIcon, RobotLabelProductivity } from '../../../../assets/brand/svg-constants';
import { AllCommunityModule, ModuleRegistry } from 'ag-grid-community';
import { AgGridReact } from 'ag-grid-react';
import '../../../../scss/InputRobotTotals.scss';
import CustomSnackbar from '../../../../components/CustomSnackbar';

ModuleRegistry.registerModules([AllCommunityModule]);

const defaultColDef = {
    flex: 1,
};

/**
 * InputRobotTotals component displays a dialog with an AG Grid table for editing robot label productivity data.
 * Allows users to select a date, view/edit operator data, and apply changes.
 * 
 * Props:
 * - open (boolean): Controls dialog visibility.
 * - onClose (function): Callback to close the dialog.
 */

const InputRobotTotals = ({ open, onClose, initialEditedRows = {} }) => {
    // Memoized style for the grid container (full width/height)
    const containerStyle = useMemo(() => ({ width: "100%", height: "91%" }), []);
    // Memoized style for the grid itself (full width/height)
    const gridStyle = useMemo(() => ({ height: "100%", width: "100%" }), []);
    // Memoized row selection mode for AG Grid (multi-row selection)
    const rowSelection = useMemo(() => ({ mode: 'multiRow' }), []);
    // State for current page size in the grid
    const [pageSize, setPageSize] = useState(10);
    // State for currently selected rows in the grid
    const [selectedRows, setSelectedRows] = useState([]);
    // State for the selected date (defaults to today)
    const [selectedDate, setSelectedDate] = useState(() => {
        // Initializes the selected date to today's date in YYYY-MM-DD format
        const today = new Date();
        return today.toISOString().slice(0, 10);
    });
    // State for tracking edited rows (for apply/update)
    const [editedRows, setEditedRows] = useState(initialEditedRows);
    // Ref to store AG Grid API instance
    const gridApi = useRef(null);
    // Snackbar state: 'none','update'
    const [snackbarType, setSnackbarType] = useState('none');

    /**
     * Handles closing the dialog.
     * Prevents closing on backdrop click or escape key.
     * Calls the onClose prop otherwise.
     * @param {object} event - The event object.
     * @param {string} reason - The reason for closing.
     */
    const handleClose = (event, reason) => {
        if (reason === 'backdropClick' || reason === 'escapeKeyDown') {
            return;
        }
        setEditedRows({});
        onClose();
    };

    // Column definitions for AG Grid (fields, headers, editable)
    const columnDefs = [
        { field: 'sNo', headerName: 'S.NO' },
        { field: 'userName', headerName: 'Operator', editable: true },
        { field: 'bundles', headerName: 'Bundles', editable: true },
        { field: 'individualLabels', headerName: 'Individual Labels', editable: true },
    ];

    /**
     * Handler for AG Grid's onGridReady event.
     * Stores the grid API reference for later use (e.g., filtering, selection).
     * @param {object} params - AG Grid event params containing the API.
     */
    const onGridReady = (params) => {
        gridApi.current = params.api;
    };



    // Store all fetched data for client-side filtering
    const [allRows, setAllRows] = useState([]);
    /**
     * Creates a datasource object for AG Grid's infinite row model.
     * Fetches data from the API and provides it to the grid.
     * Also stores the fetched rows in allRows state for client-side filtering.
     * @param {number} pageSizeArg - The number of rows per page.
     * @returns {object} datasource - AG Grid datasource with getRows method.
     */
    const createDataSource = (pageSizeArg) => ({
        getRows: (params) => {
            const page = params.startRow / pageSizeArg;
            InputRobotTotalsService.fetchInputRobotTotals(page, pageSizeArg)
                .then(data => {
                    const response = data.response.RobotLabelList;
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
                    rows = rows.map((row, idx) => ({
                        ...row,
                        sNo: params.startRow + idx + 1
                    }));

                    setAllRows(rows);
                    params.successCallback(rows, lastRow);
                })
                .catch(() => {
                    params.failCallback();
                });
        }
    });


    /**
     * Memoizes the datasource for AG Grid.
     * Re-creates the datasource whenever the pageSize changes.
     */
    const dataSource = useMemo(() => createDataSource(pageSize), [pageSize]);

    /**
     * Handler for cell value changes in AG Grid.
     * Updates the editedRows state with the changed row data.
     * @param {object} params - AG Grid cell change params (data, etc).
     */

    const handleCellValueChanged = (params) => {
        const updatedRow = params.data;
        setEditedRows(prev => ({
            ...prev,
            updatedRow
        }));
    };

    /**
     * Handler for the "Apply" button.
     * Sends updated row data to the API and closes the dialog on success.
     * If no changes, simply closes the dialog.
     * Handles errors by logging them.
     */

    const handleApply = async () => {
        if (!editedRows?.updatedRow || Object.keys(editedRows?.updatedRow).length === 0) {
            onClose();
            return;
        }
        const { sNo, reportingDate, ...rowWithoutSNo } = editedRows.updatedRow || {};
        const payload = {
            ...rowWithoutSNo,
            reportingDate: `${selectedDate}T00:00:00`
        };
        try {
            await InputRobotTotalsService.updateInputRobotTotal(payload);
            setEditedRows({});
            setSnackbarType('update');

        } catch (error) {
        }
    };

    /**
    * Cancels and closes any open snackbar.
    * Resets snackbar and add content state.
    */
    const handleSnackbarCancel = () => {
        setEditedRows({});
        setSnackbarType('none');
    };


    return (
        <>
            <Dialog open={open} onClose={handleClose} PaperProps={{ className: 'input-robot-totals-dialog' }}>
                <DialogTitle><div className="input-robot-totals-icon"><RobotLabelProductivity /></div>Robot Labels Productivity</DialogTitle>
                <IconButton
                    aria-label="close"
                    onClick={handleClose}
                    sx={{
                        position: 'absolute',
                        right: 8,
                        top: 8,
                    }}
                >
                    <CloseIcon />
                </IconButton>
                <DialogContent dividers>
                    <div className="date-layout">
                        <span className='date-text'>Date</span>
                        <CFormInput
                            type="date"
                            className='date-input'
                            value={selectedDate}
                            onChange={e => setSelectedDate(e.target.value)}
                        />
                    </div>
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
                                onCellValueChanged={handleCellValueChanged}
                            ></AgGridReact>
                        </div>
                    </div>
                </DialogContent>
                <DialogActions>
                    <div className='input-robot-totals-button-container'>
                        <Button variant="outlined" size="small" className='cancel-layout' onClick={handleClose}>Cancel</Button>
                        <Button variant="contained" size="small" className='print-layout' disabled={!editedRows?.updatedRow || Object.keys(editedRows?.updatedRow).length === 0} onClick={handleApply}>Apply</Button>
                    </div>
                </DialogActions>
            </Dialog>
            <CustomSnackbar
                type={snackbarType}
                open={snackbarType !== 'none'}
                handleOk={handleSnackbarCancel}
                onClose={handleSnackbarCancel}
                title={
                    snackbarType === 'update' ? 'Robot Labels Productivity' : ''
                }
                body={
                    snackbarType === 'update' ? 'You have successfully Updated a client' : ''
                }
            />
        </>
    );
};

export default InputRobotTotals;
