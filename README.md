{
  "response": {
    "listEmails": [
      {
        "name": "test1209                 ",
        "emailAddress": "sowmya@gmail.com                                  ",
        "application": "TRACE                                             ",
        "startHour": 1,
        "endHour": 1,
        "days": "M,T,W,Th       ",
        "hostId": 1,
        "activeInactive": "Y"
      },
      {
        "name": "e                        ",
        "emailAddress": "                                                  ",
        "application": "\"99999                                            ",
        "startHour": 1,
        "endHour": 1,
        "days": "M              ",
        "hostId": 1,
        "activeInactive": "Y"
      },
      {
        "name": "jyoti regmi              ",
        "emailAddress": "jyoti.regmi@firstdata.com                         ",
        "application": "test                                              ",
        "startHour": 0,
        "endHour": 23,
        "days": "M,T,W,Th,F,S,Su",
        "hostId": 1,
        "activeInactive": "N"
      },
      {
        "name": "Melissa Wait             ",
        "emailAddress": "melissa.wait@FirstDataCorp.com                    ",
        "application": "MailingSysPrin                                    ",
        "startHour": 0,
        "endHour": 23,
        "days": "Su,M,T,W,Th,F,S",
        "hostId": 1,
        "activeInactive": "N"
      },
      {
        "name": "Sandra Grant             ",
        "emailAddress": "sandra.grant@Chase.com                            ",
        "application": "ChaseDebitReturns                                 ",
        "startHour": 0,
        "endHour": 23,
        "days": "Su,M,T,W,Th,F,S",
        "hostId": 1,
        "activeInactive": "Y"
      },
      {
        "name": "Larry Rosen              ",
        "emailAddress": "Larry.A.Rosen@chase.com                           ",
        "application": "ChaseDebitReturns                                 ",
        "startHour": 0,
        "endHour": 23,
        "days": "Su,M,T,W,Th,F,S",
        "hostId": 1,
        "activeInactive": "Y"
      },
      {
        "name": "Euclid Mahon             ",
        "emailAddress": "Euclid.mahon@chase.com                            ",
        "application": "ChaseDebitReturns                                 ",
        "startHour": 0,
        "endHour": 23,
        "days": "Su,M,T,W,Th,F,S",
        "hostId": 1,
        "activeInactive": "Y"
      },
      {
        "name": "TRJHGYJ                  ",
        "emailAddress": "ann.clark@FirstDataCorp.com                       ",
        "application": "MailingSysPrin                                    ",
        "startHour": 0,
        "endHour": 23,
        "days": "Th,F,S         ",
        "hostId": 1,
        "activeInactive": "N"
      },
      {
        "name": "Melanie Canby            ",
        "emailAddress": "melanie.canby@FirstDataCorp.com                   ",
        "application": "MailingSysPrin                                    ",
        "startHour": 0,
        "endHour": 23,
        "days": "Su,M,T,W,Th,F,S",
        "hostId": 1,
        "activeInactive": "N"
      },
      {
        "name": "Vicky Schuman            ",
        "emailAddress": "vicky.schuman@firstdata.com                       ",
        "application": "test                                              ",
        "startHour": 0,
        "endHour": 23,
        "days": "M,T,W,Th,F,S,Su",
        "hostId": 1,
        "activeInactive": "N"
      }
    ]
  },
  "message": "EmailSetup List fetched successfully10"
}



import React, { useState, useEffect, useMemo, useRef } from 'react';

import { ModuleRegistry } from 'ag-grid-community';
import { ClientSideRowModelModule, InfiniteRowModelModule } from 'ag-grid-community';

import { AgGridReact } from 'ag-grid-react';
import TitleContainer from '../../../components/TittleContainer';
import * as emailSetupService from '../../../services/AdminEditService/EmailSetupService';
import WeekDaysRenderer from './WeekDays';
import EmailSetupDialog from './EmailSetupDialog';
import '../../../scss/EmailSetup.scss';
import CustomSnackbar from '../../../components/CustomSnackbar';
import { EditIcon, ClientMappingDeleteIcon, SearchIcon } from '../../../assets/brand/svg-constants';


ModuleRegistry.registerModules([ClientSideRowModelModule, InfiniteRowModelModule]);

const EmailSetup = () => {
  const containerStyle = useMemo(() => ({ width: "100%", height: "543px", paddingTop: "16px" }), []);
  const gridStyle = useMemo(() => ({ height: "100%", width: "100%" }), []);
  const rowSelection = useMemo(() => ({ mode: 'multiRow' }), []);
  const [pageSize, setPageSize] = useState(10);
  // Store all fetched data for client-side filtering
  const [allRows, setAllRows] = useState([]);
  const gridApi = useRef(null);
  const [loading, setLoading] = useState(true);
  const [refreshKey, setRefreshKey] = useState(0);
  const [selectedRows, setSelectedRows] = useState([]);
  const [isAddEmailSetupDialog, setIsAddEmailSetupDialog] = useState(false);
  const [selectedEditRecord, setSelectedEditRecord] = useState({});
  const [snackbarType, setSnackbarType] = useState('none');
  const [totalCount, setTotalCount] = useState(0);



  const [columnDefs, setColumnDefs] = useState([
    { field: 'aspId', headerName: '#',minWidth: 75 },
    { field: 'activeInactive', headerName: 'Indicator',minWidth: 97},
    { field: 'name', headerName: 'Name' },
    { field: 'emailAddress', headerName: 'Email' },
    { field: 'application', headerName: 'Application' },
    { field: 'startHour', headerName: 'Start Hr', minWidth: 50 },
    { field: 'endHour', headerName: 'End Hr', minWidth: 50 },
    { field: 'days', headerName: 'Week Days',cellRenderer: WeekDaysRenderer,cellClass: 'days-cell',minWidth: 150,},
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
    }
  ]);
  const defaultColDef = useMemo(() => {
    return {
      flex: 1,
      minWidth: 100,
    };
  }, []);

  /**
   * Handler for the Edit icon click in the grid.
   * Opens the dialog and sets the selected record for editing.
   * @param {object} rowData - The data of the row to edit.
   */
  const handleEditClick = (rowData) => {
    setIsAddEmailSetupDialog(true);
    setSelectedEditRecord(rowData)
  }


  /**
   * Handler for the Delete icon click in the grid.
   * Adds the selected row to the selectedRows state and opens the delete snackbar.
   * @param {object} rowData - The data of the row to delete.
   */
  const handleDeleteClick = async (rowData) => {
    setSelectedRows(prev => [...prev, rowData]);
    setSnackbarType('delete');
  }

  /**
    * Handler for AG Grid's onGridReady event.
    * Stores the grid API reference for later use (e.g., filtering, selection).
    * @param {object} params - AG Grid event params containing the API.
    */
  const onGridReady = (params) => {
    gridApi.current = params.api;
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
      emailSetupService.fetchEmailSetup(page, pageSizeArg)
        .then(data => {
          const totalCount = data.message.split(':')[1].trim();
          setTotalCount(totalCount);
          const emailSetupData = data.response.listEmails
          let rows, lastRow;
          if (emailSetupData && Array.isArray(emailSetupData.rows) && typeof emailSetupData.totalElements === 'number') {
            rows = emailSetupData.rows;
            lastRow = emailSetupData.totalElements;
          } else if (Array.isArray(emailSetupData) && typeof emailSetupData.totalElements === 'number') {
            rows = emailSetupData;
            lastRow = emailSetupData.totalElements;
          } else if (Array.isArray(emailSetupData)) {
            rows = emailSetupData;
            lastRow = rows.length < pageSizeArg ? params.startRow + rows.length : -1;
          } else {
            rows = [];
            lastRow = 0;
          }
          setAllRows(rows);
          setLoading(false);
          console.log('AG Grid getRows callback', { page, pageSizeArg, rows, lastRow, emailSetupData });
          params.successCallback(rows, lastRow);
        })
        .catch(() => {
          setLoading(false);
          params.failCallback();
        });
    }
  });


  // Re-set datasource when pageSize changes (for infinite row model)
  const dataSource = useMemo(() => createDataSource(pageSize), [pageSize]);

  /**
   * Opens the dialog for adding a new email setup record.
   */
  const showAddDialog = () => {
    setSelectedEditRecord({});
    setIsAddEmailSetupDialog(true);
  }

  // Add a handler to be called after add/update success
  /**
   * Handler to be called after a successful add or update operation in the dialog.
   * Closes the dialog, shows the snackbar, and refreshes the grid.
   * @param {string} type - The type of operation ('add' or 'update').
   */
  const handleDialogSuccess = async (type) => {
    setIsAddEmailSetupDialog(false);
    setSnackbarType(type);
    setRefreshKey(prev => prev + 1);

  };

  /**
   * Handles the Cancel/Close action for any snackbar.
   * Resets the snackbarType to 'none'.
   */
  const handleSnackbarCancel = () => {
    setSelectedEditRecord({});
    setSelectedRows([]);
    setSnackbarType('none');
  };

  /**
       * Handles the OK action in the delete confirmation snackbar.
       * Calls the delete API, clears the form, and shows the success snackbar.
       */
  const handleSnackbarOk = async () => {
    setSnackbarType('none');
    const selectedDeletedEmailSetup = selectedRows
      .map(row => row.aspId)
      .filter(Boolean);
    const payload = { aspId: selectedDeletedEmailSetup };
    try {
      // Pass the array directly to the delete API
      await emailSetupService.deleteEmailSetup(payload);
      setSnackbarType('delete-confirmation');
      setRefreshKey(prev => prev + 1);
    } catch (error) {
      // Handle error if needed
    }
  };

  return (
    <div>
      <TitleContainer
        title="Email Setup"
        hideSaveButton={true}
        hideUpdateButton={false}
        onAdd={showAddDialog}
        hideDeleteButton={selectedRows.length > 1 ? true : false}
        selectedDeletedRows={selectedRows}
        onDelete={handleDeleteClick}
        totalCount={totalCount}
      />
      {selectedRows.length > 0 && (
        <div>
          <span className='text-count'>{selectedRows.length} Rows selected</span>
        </div>
      )}
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
      <EmailSetupDialog open={isAddEmailSetupDialog} onClose={() => setIsAddEmailSetupDialog(false)} selectedEditRecords={selectedEditRecord}
        onSuccess={handleDialogSuccess} />
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
              ? 'You have successfully Added a new Email setup'
              : snackbarType === 'update'
                ? 'You have successfully Updated an Email setup'
                : snackbarType === 'delete-confirmation'
                  ? 'You have successfully Deleted an Email setup'
                  : ''
          }
        />
      )}
    </div>
  )
}

export default EmailSetup






