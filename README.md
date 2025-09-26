import React, { useState, useMemo, useRef , useEffect} from 'react';
import { Dialog, DialogTitle, DialogContent, DialogActions, Button } from '@mui/material';
import IconButton from '@mui/material/IconButton';
import { CloseIcon, EditIcon, ClientMappingDeleteIcon, SearchIcon } from '../../../assets/brand/svg-constants';
import { CFormTextarea, CFormInput, CFormCheck, CFormSelect } from '@coreui/react';
import { fetchClientReportMappings, deleteClientReportMapping, updateClientReportMapping, addClientReportMapping, searchByClientId, deleteAllClientReportMapping } from '../../../services/AdminMaintenanceService/ClientReportMappingService';
import { AgGridReact } from "ag-grid-react";
import { ModuleRegistry, AllCommunityModule } from "ag-grid-community";
import CustomSnackbar from '../../../components/CustomSnackbar';
import '../../../scss/ClientReportMapping.scss';

ModuleRegistry.registerModules([
  AllCommunityModule,
]);

const ClientReportMapping = ({ open, onClose }) => {
  const containerStyle = useMemo(() => ({ width: "100%", height: "89%" }), []);
  const gridStyle = useMemo(() => ({ height: "100%", width: "100%" }), []);



  const [columnDefs, setColumnDefs] = useState([
    {
      field: 'clientId',
      headerName: 'Client',
      maxWidth: 100,
    },
    { field: 'reportClientId', headerName: 'Report Client', maxWidth: 188 },
    // sendToBothInd column will be conditionally added after data is loaded
    { field: 'description', headerName: 'Description', minWidth: 143 },
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
  const [infoSnackbarOpen, setInfoSnackbarOpen] = useState(false);
  // Snackbar state: 'none', 'add', 'update', 'delete', 'info'
  const [snackbarType, setSnackbarType] = useState('none');
  const [searchValue, setSearchValue] = useState('');

  const [refreshKey, setRefreshKey] = useState(0);

  const [selectedRowToDelete, setSelectedRowToDelete] = useState(null);
  const [data, setData] = useState({
    clientId: '',
    reportClientId: '',
    sendToBothInd: false,
    application: '',
    description: ''
  });
  const [originalData, setOriginalData] = useState(null);
  const [enableAddContent, setEnableAddContent] = useState(false);
  const [pageSize, setPageSize] = useState(10);
  const gridApi = useRef(null);

  const applicationDropdownOptions = [
    { label: '', value: '' },
    { label: 'Dialer', value: 'Dialer' },
    { label: 'Rapid', value: 'Rapid' },
  ];



  /**
   * Clears the form and resets the data and originalData state to their initial values.
   * Used when opening the add dialog or after a successful add/update/delete.
   */
  const onHandleClear = () => {
    setOriginalData(null);
    setSelectedRows([]);
    setData({
      clientId: '',
      reportClientId: '',
      sendToBothInd: false,
      application: '',
      description: ''
    });
  };


  // Store all fetched data for client-side filtering
  const [allRows, setAllRows] = useState([]);
  /**
   * Creates a datasource object for AG Grid's infinite row model.
   * Fetches data from the API and provides it to the grid.
   * Also stores the fetched rows in allRows state for client-side filtering.
   * @param {number} pageSizeArg - The number of rows per page.
   */
  const createDataSource = (pageSizeArg) => ({
    getRows: (params) => {
      const page = params.startRow / pageSizeArg;
      fetchClientReportMappings(page, pageSizeArg)
        .then(data => {
          const clientReportMappingData = data.response.clientReportMapping;
          let rows, lastRow;
          if (clientReportMappingData && Array.isArray(clientReportMappingData.rows) && typeof clientReportMappingData.totalElements === 'number') {
            rows = clientReportMappingData.rows;
            lastRow = clientReportMappingData.totalElements;
          } else if (Array.isArray(clientReportMappingData) && typeof clientReportMappingData.totalElements === 'number') {
            rows = clientReportMappingData;
            lastRow = clientReportMappingData.totalElements;
          } else if (Array.isArray(clientReportMappingData)) {
            rows = clientReportMappingData;
            lastRow = rows.length < pageSizeArg ? params.startRow + rows.length : -1;
          } else {
            rows = [];
            lastRow = 0;
          }
          setAllRows(rows);
          console.log('AG Grid getRows callback', { page, pageSizeArg, rows, lastRow, clientReportMappingData });
          params.successCallback(rows, lastRow);
        })
        .catch(() => {
          params.failCallback();
        });
    }
  });
  useEffect(() => {
    setDataSource(() => createDataSource(pageSize));
    setRefreshKey(prev => prev + 1);
    // eslint-disable-next-line
  }, [pageSize]);

  /**
   * Handler for the Add button click.
   * Clears the form and enables the add content dialog.
   */
  const handleAddClick = () => {
    onHandleClear();
    setEnableAddContent(true);
  }

  /**
   * Handler for confirming a delete action.
   * Calls the delete API for the selected row and clears the form.
   */
  const onHandleDelete = async() => {
    setDeleteSnackbarOpen(false);
    if(selectedRows.length > 1){
      try {
        const filteredData = selectedRows.map(({ clientId, description }) => ({ clientId, description }));
        await deleteAllClientReportMapping(filteredData);
        onHandleClear();
        setSnackbarType('delete-confirmation');
        if (gridApi.current) {
          gridApi.current.refreshInfiniteCache();
        }
      } catch (err) {
      }

    } else {
      if (selectedRowToDelete.clientId) {
      try {
        let application = selectedRowToDelete.description.split('-')[0]
        await deleteClientReportMapping(selectedRowToDelete.clientId, application);
        onHandleClear();
        setSnackbarType('delete-confirmation');
        if (gridApi.current) {
          gridApi.current.refreshInfiniteCache();
        }
      } catch (err) {
      }
    }
    }
    
  }
  



  /**
   * Handler for the Edit icon click in the grid.
   * Populates the form with the selected row's data and enables edit mode.
   * @param {object} rowData - The data of the row to edit.
   */
  const handleEditClick = (rowData) => {
    setEnableAddContent(true);
    const [application, description] = rowData.description.split('-');
    const formData = {
      clientId: rowData.clientId,
      reportClientId: rowData.reportClientId,
      sendToBothInd: rowData.sendToBothInd,
      application: application,
      description: description,
    };
    setData(formData);
    setOriginalData(formData);
  };

  /**
   * Handler for the Delete icon click in the grid.
   * Opens the delete confirmation snackbar for the selected row.
   * @param {object} rowData - The data of the row to delete.
   */
  const handleDeleteClick = (rowData) => {
    setSelectedRowToDelete(rowData);
    setDeleteSnackbarOpen(true);
  };

  /**
   * Handler for the Save button in the add/edit dialog.
   * Calls onUpdate if editing, or onAdd if adding a new record.
   */
  const handleSave = async () => {
    if (originalData) {
      await onUpdate();
    } else {
      await onAdd();
    }
  }

  /**
   * Calls the update API to update an existing client report mapping.
   * Shows info snackbar if validation fails, otherwise shows update snackbar on success.
   */
  const onUpdate = async () => {
    if (data.clientId === data.reportClientId && data.sendToBothInd) {
      setSnackbarType('info');
      return;
    }
    try {
      await updateClientReportMapping(data);
      setSnackbarType('update');
      setTimeout(() => onHandleClear(), 500);
      if (gridApi.current) {
        gridApi.current.refreshInfiniteCache();
      }
    } catch (err) {
    }
  }

  /**
   * Calls the add API to add a new client report mapping.
   * Shows success snackbar on success.
   */
  const onAdd = async () => {
    if (data.clientId === data.reportClientId && data.sendToBothInd) {
      setSnackbarType('info');
      return;
    }
    try {
      await addClientReportMapping(data);
      setSnackbarType('add');
      setTimeout(() => onHandleClear(), 500);
    } catch (err) {
    }
  }

  /**
   * Checks if the form data has changed compared to the original data.
   * Used to enable/disable the Save button.
   * @returns {boolean} True if data is changed and valid, false otherwise.
   */
  const isDataChanged = () => {
    if (!originalData) {
      return (
        (data.clientId.length === 4 &&
          data.reportClientId.length === 4 &&
          data.application.trim() !== '' &&
          data.description.trim() !== '') && (data.clientId !== data.reportClientId ? data.sendToBothInd : true)
      );
    }

    if (data.clientId.length !== 4 || data.reportClientId.length !== 4) {
      return false;
    }
    return JSON.stringify(data) !== JSON.stringify(originalData);
  };

  /**
   * Handler for closing the dialog.
   * Prevents closing on backdrop click or escape key.
   * Resets add/edit state and calls the parent onClose.
   */
  const handleClose = (event, reason) => {
    if (reason === 'backdropClick' || reason === 'escapeKeyDown') {
      return;
    }
    setEnableAddContent(false);
    onClose();
  };

  /**
   * Handler for closing any open snackbar.
   * Resets all snackbar open states to false.
   */
  const handleSnackbarCancel = () => {
    setDeleteSnackbarOpen(false);
    setEnableAddContent(false);
    setSnackbarType('none');
  };

  /**
   * Handler for AG Grid's onGridReady event.
   * Stores the grid API reference for later use (e.g., filtering, selection).
   * @param {object} params - AG Grid event params containing the API.
   */
  const onGridReady = (params) => {
    gridApi.current = params.api;
  };

  // Re-set datasource when pageSize changes (for infinite row model)
  // const dataSource = useMemo(() => createDataSource(pageSize), [pageSize]);

  const handleSearchChange = async (value) => {
    setSearchValue(value);

    if (value.trim() === '') {
      // Restore original datasource
      setDataSource(() => createDataSource(pageSize));
      setRefreshKey(prev => prev + 1);
      return;
    }

    // Create a new datasource for the search
    const searchDataSource = {
      getRows: (params) => {
        searchByClientId(value)
          .then(result => {
            const resultData = result.response.clientReportMapping || [];
            params.successCallback(resultData, resultData.length);
          })
          .catch(() => {
            params.failCallback();
          });
      }
    };

    setDataSource(() => searchDataSource);
    setRefreshKey(prev => prev + 1);
  };

  /**
   * Handler for Delete All button click.
   * Opens the delete confirmation snackbar for all selected rows.
   */
  const handleDeleteAll =  () => {
    setDeleteSnackbarOpen(true);
  }

  const [dataSource, setDataSource] = useState(() => createDataSource(pageSize));



  return (
    <>
      <Dialog open={open} onClose={handleClose} PaperProps={{ className: 'client-report-mapping-dialog' }}>
        <DialogTitle>Trace Admin Client Report Mapping</DialogTitle>
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
                  onClick={() => handleDeleteAll()}
                >
                  Delete
                </Button>
              </div>
            ) : (
              <>
                <div className='search-input'>
                  <CFormInput
                    placeholder='Search' value={searchValue}
                    onChange={e => handleSearchChange(e.target.value)}
                  />
                  <span className='search-icon'>
                    <SearchIcon style={{ cursor: 'pointer' }} onClick={() => handleSearchChange(searchValue)} />
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
                <div className='clientId-container client-field'>
                  <span className='label' data-testid='client-id-label'>Client ID</span>
                  <CFormInput
                    type='text'
                    value={data.clientId}
                    onChange={(e) => setData({ ...data, clientId: e.target.value })}
                    maxLength={4}
                    className='clientId-input'
                  />
                </div>
                <div className='clientId-container client-field'>
                  <span className='label' data-testid="report-client-id-label">Report Client ID</span>
                  <CFormInput
                    type='text'
                    value={data.reportClientId}
                    onChange={(e) => setData({ ...data, reportClientId: e.target.value })}
                    maxLength={4}
                    className='clientId-input'
                  />
                </div>

              </div>
              <div className='row-container'>
                <div className='clientId-container client-field'>
                  <span className='label'>Application</span>
                  <CFormSelect className='clientId-input'
                    value={data.application}
                    onChange={(e) => setData({ ...data, application: e.target.value })}
                  >
                    {applicationDropdownOptions.map((option) => (
                      <option key={option.value} value={option.value}>
                        {option.label}
                      </option>
                    ))}
                  </CFormSelect>
                </div>
                <div className='send-to-both-container'>
                  <CFormCheck className="send-to-both" disabled={data.clientId === data.reportClientId} checked={data.sendToBothInd} onChange={(e) => setData({ ...data, sendToBothInd: e.target.checked })}></CFormCheck>
                  <span>Send to Both</span>
                </div>
              </div>
              <div className='second-row-container'>

                <div className='clientId-container'>
                  <span data-testid="description-label">Description</span>
                  <CFormTextarea
                    value={data.description}
                    onChange={(e) => setData({ ...data, description: e.target.value })}
                    className='description-textarea'
                  />
                </div>
              </div>
            </div>
          ) : (<div style={containerStyle}>
            <div style={gridStyle}>
              <AgGridReact
                key={refreshKey}
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
          </div>)
          }
        </DialogContent >
        {enableAddContent ? (<DialogActions className='add-action-container'>
          <div className='client-report-mapping-button-container'>
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
              snackbarType === 'delete-confirmation' ? 'Client Report Mapping' :
                snackbarType === 'info' ? 'Confirm Action' : ''
        }
        body={
          snackbarType === 'add' ? 'You have successfully Added a new client' :
            snackbarType === 'update' ? 'You have successfully Updated a client' :
              snackbarType === 'delete-confirmation' ? 'You have successfully Deleted a client' :
                snackbarType === 'info' ? 'Send To Both is invalid when Client ID equals Report Client ID' : ''
        }
      />
    </>
  );
};

export default ClientReportMapping;
