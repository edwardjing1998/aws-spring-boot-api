import React, { useState, useMemo, useRef, useEffect } from 'react';
import { ModuleRegistry, AllCommunityModule } from 'ag-grid-community';
import { AgGridReact } from 'ag-grid-react';
import { Button } from '@mui/material';
import { CFormTextarea, CFormInput, CFormCheck, CFormSelect } from '@coreui/react';
import CustomSnackbar from '../../../components/CustomSnackbar';
import { EditIcon, ClientMappingDeleteIcon, SearchIcon } from '../../../assets/brand/svg-constants';
import { clientReportMappingData } from './data';
import '../../../scss/ClientReportMapping.scss';

// Register AG Grid community module
ModuleRegistry.registerModules([AllCommunityModule]);

const ClientReportMapping = () => {
  // Give the grid a known height so it renders even if parents lack explicit heights.
  const containerStyle = useMemo(() => ({ width: '100%', height: 500 }), []);
  const gridStyle = useMemo(() => ({ height: '100%', width: '100%' }), []);
  const defaultColDef = useMemo(() => ({ flex: 1, minWidth: 100, sortable: true, filter: true }), []);

  const gridApi = useRef(null);

  // Client-side data
  const [rowData, setRowData] = useState([]);

  useEffect(() => {
    const rows = clientReportMappingData?.response?.clientReportMapping ?? [];
    setRowData(rows.map((r, i) => ({ id: i + 1, ...r })));
  }, []);

  // Columns for your JSON
  const [columnDefs] = useState([
    { field: 'clientId', headerName: 'Client', maxWidth: 120 },
    { field: 'reportClientId', headerName: 'Report Client', maxWidth: 160 },
    {
      field: 'sendToBothInd',
      headerName: 'Send To Both',
      maxWidth: 140,
      valueGetter: (p) => (p.data?.sendToBothInd ? 'Yes' : 'No'),
    },
    { field: 'description', headerName: 'Description', minWidth: 200, flex: 2 },
    {
      headerName: '',
      field: 'actions',
      minWidth: 120,
      cellClass: 'actions-cell-flex-end',
      cellRenderer: (params) => (
        <div className="actions-cell icon-container action-cell-flex">
          <span className="icon-wrapper">
            <EditIcon style={{ cursor: 'pointer' }} onClick={() => handleEditClick(params.data)} />
          </span>
          <span className="icon-wrapper">
            <ClientMappingDeleteIcon
              style={{ cursor: 'pointer' }}
              onClick={() => handleDeleteClick(params.data)}
            />
          </span>
        </div>
      ),
    },
  ]);

  // Toolbar & form state
  const [selectedRows, setSelectedRows] = useState([]);
  const [snackbarType, setSnackbarType] = useState('none'); // 'add' | 'update' | 'delete-confirmation' | 'none'
  const [enableAddContent, setEnableAddContent] = useState(false);
  const [searchValue, setSearchValue] = useState('');

  const [data, setData] = useState({
    clientId: '',
    reportClientId: '',
    sendToBothInd: false,
    application: '',
    description: '',
  });
  const [originalData, setOriginalData] = useState(null);

  const applicationDropdownOptions = [
    { label: '', value: '' },
    { label: 'Dialer', value: 'Dialer' },
    { label: 'Rapid', value: 'Rapid' },
  ];

  const onHandleClear = () => {
    setOriginalData(null);
    setSelectedRows([]);
    setData({ clientId: '', reportClientId: '', sendToBothInd: false, application: '', description: '' });
  };

  const handleAddClick = () => {
    onHandleClear();
    setEnableAddContent(true);
  };

  const handleEditClick = (row) => {
    setEnableAddContent(true);
    const [application, ...rest] = (row.description || '').split('-');
    const form = {
      clientId: row.clientId,
      reportClientId: row.reportClientId,
      sendToBothInd: row.sendToBothInd,
      application: application || '',
      description: rest.join('-').trim() || row.description || '',
    };
    setData(form);
    setOriginalData(form);
  };

  const handleDeleteClick = (row) => {
    // With static data, just remove locally and show snackbar
    setRowData((prev) => prev.filter((r) => !(r.clientId === row.clientId && r.reportClientId === row.reportClientId)));
    setSnackbarType('delete-confirmation');
  };

  const handleSave = () => {
    if (originalData) {
      // update in-place
      setRowData((prev) =>
        prev.map((r) =>
          r.clientId === originalData.clientId && r.reportClientId === originalData.reportClientId
            ? { ...r, ...data, description: `${data.application ? data.application + '-' : ''}${data.description}` }
            : r
        ),
      );
      setSnackbarType('update');
    } else {
      // add new
      const newRow = {
        clientId: data.clientId,
        reportClientId: data.reportClientId,
        sendToBothInd: data.sendToBothInd,
        description: `${data.application ? data.application + '-' : ''}${data.description}`,
      };
      setRowData((prev) => [newRow, ...prev]);
      setSnackbarType('add');
    }
    setEnableAddContent(false);
  };

  const isDataChanged = () => {
    if (!originalData) {
      return data.clientId.length === 4 && data.reportClientId.length === 4 && data.description.trim() !== '';
    }
    return JSON.stringify(data) !== JSON.stringify(originalData);
  };

  const handleSearchChange = (value) => {
    setSearchValue(value);
    // quick client-side grid search
    gridApi.current?.setQuickFilter(value);
  };

  return (
    <div className="client-report-mapping-page client-report-mapping-dialog">
      {/* Toolbar */}
      {!enableAddContent && (
        <div className="crm-toolbar action-container" style={{ padding: '12px 0 50px' }}>
          <div className="search-input">
            <CFormInput
              placeholder="Search"
              value={searchValue}
              onChange={(e) => handleSearchChange(e.target.value)}
            />
            <span className="search-icon">
              <SearchIcon style={{ cursor: 'pointer' }} onClick={() => handleSearchChange(searchValue)} />
            </span>
          </div>
          <div>
            <Button variant="contained" size="small" onClick={handleAddClick}>
              Add
            </Button>
          </div>
        </div>
      )}

      {/* Content */}
      <div className="crm-content">
        {enableAddContent ? (
          <>
            <h2 style={{ margin: '0 0 12px' }}>Add / Edit Client Report Mapping</h2>
            <div className="add-main-container">
              <div className="row-container">
                <div className="clientId-container client-field">
                  <span className="label">Client ID</span>
                  <CFormInput
                    type="text"
                    maxLength={4}
                    value={data.clientId}
                    onChange={(e) => setData({ ...data, clientId: e.target.value })}
                    className="clientId-input"
                  />
                </div>
                <div className="clientId-container client-field">
                  <span className="label">Report Client ID</span>
                  <CFormInput
                    type="text"
                    maxLength={4}
                    value={data.reportClientId}
                    onChange={(e) => setData({ ...data, reportClientId: e.target.value })}
                    className="clientId-input"
                  />
                </div>
              </div>

              <div className="row-container">
                <div className="clientId-container client-field">
                  <span className="label">Application</span>
                  <CFormSelect
                    className="clientId-input"
                    value={data.application}
                    onChange={(e) => setData({ ...data, application: e.target.value })}
                  >
                    {applicationDropdownOptions.map((o) => (
                      <option key={o.value} value={o.value}>
                        {o.label}
                      </option>
                    ))}
                  </CFormSelect>
                </div>
                <div className="send-to-both-container">
                  <CFormCheck
                    className="send-to-both"
                    checked={data.sendToBothInd}
                    onChange={(e) => setData({ ...data, sendToBothInd: e.target.checked })}
                  />
                  <span>Send to Both</span>
                </div>
              </div>

              <div className="second-row-container">
                <div className="clientId-container">
                  <span>Description</span>
                  <CFormTextarea
                    value={data.description}
                    onChange={(e) => setData({ ...data, description: e.target.value })}
                    className="description-textarea"
                  />
                </div>
              </div>

              <div className="add-action-container client-report-mapping-button-container" style={{ marginTop: 12 }}>
                <Button
                  variant="outlined"
                  size="small"
                  onClick={() => {
                    setEnableAddContent(false);
                    onHandleClear();
                  }}
                >
                  Cancel
                </Button>
                <Button variant="contained" size="small" disabled={!isDataChanged()} onClick={handleSave}>
                  Save
                </Button>
              </div>
            </div>
          </>
        ) : (
          <div className="ag-theme-quartz" style={containerStyle}>
            <div style={gridStyle}>
              <AgGridReact
                rowData={rowData}
                columnDefs={columnDefs}
                defaultColDef={defaultColDef}
                pagination={true}
                paginationPageSize={10}
                paginationPageSizeSelector={[10, 20, 50, 100]}
                rowSelection="multiple"
                onGridReady={(params) => {
                  gridApi.current = params.api;
                }}
                onSelectionChanged={(p) => setSelectedRows(p.api.getSelectedRows())}
              />
            </div>
          </div>
        )}
      </div>

      {/* Snackbars */}
      <CustomSnackbar
        type={snackbarType}
        open={snackbarType !== 'none'}
        handleOk={() => setSnackbarType('none')}
        onClose={() => setSnackbarType('none')}
        title={
          snackbarType === 'add' || snackbarType === 'update' || snackbarType === 'delete-confirmation'
            ? 'Client Report Mapping'
            : ''
        }
        body={
          snackbarType === 'add'
            ? 'You have successfully Added a new client'
            : snackbarType === 'update'
            ? 'You have successfully Updated a client'
            : snackbarType === 'delete-confirmation'
            ? 'You have successfully Deleted a client'
            : ''
        }
      />
    </div>
  );
};

export default ClientReportMapping;



import React, { useEffect, useState } from 'react';
import Dialog from '@mui/material/Dialog';
import DialogTitle from '@mui/material/DialogTitle';
import DialogContent from '@mui/material/DialogContent';
import DialogActions from '@mui/material/DialogActions';
import Button from '@mui/material/Button';
import IconButton from '@mui/material/IconButton';
import { CloseIcon } from '../../../assets/brand/svg-constants';
import * as emailSetupService from '../../../services/AdminEditService/EmailSetupService';
import { CFormInput, CFormSelect, CFormCheck } from '@coreui/react';
import CustomSnackbar from '../../../components/CustomSnackbar';
import '../../../scss/EmailSetup.scss';
import { set } from 'lodash';

const EmailSetupDialog = ({ open, onClose, selectedEditRecords,onSuccess }) => {

    const [emaiSetupDetails, setemaiSetupDetails] = useState({
        aspId: '',
        name: '',
        emailAddress: '',
        application: '',
        startHour: '',
        endHour: '',
        hostId: '',
        activeInactive: '',
        days: {
            Su: false,
            M: false,
            T: false,
            W: false,
            Th: false,
            F: false,
            S: false,
        },
    });
    const [originalEmailSetupData, setOriginalEmailSetupData] = useState(null);
    const [applicationDropdownDetails, setapplicationDropdownDetails] = useState([]);
    const [emailServerDropdownDetails, setemailServerDropdownDetails] = useState([]);
    const [snackbarType, setSnackbarType] = useState('none');

    useEffect(() => {
        if (open) {
            getApplicationDropdownDetails();
            getEmailServerDropdownDetails();
            if (selectedEditRecords && Object.keys(selectedEditRecords).length > 0) {
                const formatedData = {
                    aspId: selectedEditRecords.aspId || '',
                    name: selectedEditRecords.name || '',
                    emailAddress: selectedEditRecords.emailAddress || '',
                    application: selectedEditRecords.application || '',
                    startHour: selectedEditRecords.startHour || '',
                    endHour: selectedEditRecords.endHour || '',
                    hostId: selectedEditRecords.hostId || '',
                    activeInactive: selectedEditRecords.activeInactive === 'Y' ? true : false,
                    days: parseDaysString(selectedEditRecords.days)
                }
                setemaiSetupDetails(formatedData)
                setOriginalEmailSetupData(formatedData);
            } else {
                setemaiSetupDetails({
                    aspId: '',
                    name: '',
                    emailAddress: '',
                    application: '',
                    startHour: '',
                    endHour: '',
                    hostId: '',
                    activeInactive: false,
                    days: {
                        Su: false,
                        M: false,
                        T: false,
                        W: false,
                        Th: false,
                        F: false,
                        S: false,
                    },
                });
                setOriginalEmailSetupData(null);
            }       

        }

    }, [open]);

    /**
     * Converts a comma-separated string of day abbreviations into an object with boolean values for each day.
     * @param {string} daysString - Comma-separated string of day abbreviations (e.g., 'M,T,W')
     * @returns {object} Object with day abbreviations as keys and booleans as values
     */
    function parseDaysString(daysString) {
        const dayMap = {
            Su: false,
            M: false,
            T: false,
            W: false,
            Th: false,
            F: false,
            S: false,
        };
        const selected = daysString.split(',').map(d => d.trim());
        selected.forEach(abbr => {
            dayMap[abbr] = true;
        });
        return dayMap;
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


    /**
   * Handler for closing any open snackbar.
   * Resets all snackbar open states to false.
   */
    const handleSnackbarCancel = () => {
        setSnackbarType('none');
    };

    /**
     * Fetches application dropdown values from the API and updates state.
     */
    const getApplicationDropdownDetails = async () => {
        const response = await emailSetupService.fetchApplicationDropdownValues();
        setapplicationDropdownDetails(response.response.EventIdList);
    }

    /**
     * Fetches email server dropdown values from the API and updates state.
     */
    const getEmailServerDropdownDetails = async () => {
        const response = await emailSetupService.fetchEmailServerDropdownValues();
        setemailServerDropdownDetails(response.response.EmailHostAddressList);
    }
    /**
     * Sets all days in the days object to true (selects all days).
     */
    const handleSelectAllDays = () => {
        setemaiSetupDetails((prev) => ({
            ...prev,
            days: Object.keys(prev.days).reduce((acc, day) => {
                acc[day] = true
                return acc
            }, {}),
        }))
    }

    /**
     * Handles changes to all form inputs and checkboxes, updating the corresponding state.
     * @param {object} e - The event object from the input/select/change.
     */
    const handleChange = (e) => {
        const { name, value, type, checked } = e.target
        if (emaiSetupDetails.days.hasOwnProperty(name)) {
            setemaiSetupDetails((prev) => ({
                ...prev,
                days: {
                    ...prev.days,
                    [name]: checked,
                },
            }))
        } else {
            setemaiSetupDetails((prev) => ({
                ...prev,
                [name]: type === 'checkbox' ? checked : value,
            }))
        }
    }

    /**
     * Determines if the update button should be enabled based on changes and validation.
     * @returns {boolean} True if update is allowed, false otherwise.
     */
    const isUpdateEnabled = () => {
        if (!originalEmailSetupData) return false;
        if (Number(emaiSetupDetails.endHour) < Number(emaiSetupDetails.startHour)) {
            return false;
        }
        return (
            emaiSetupDetails.name.trim() !== originalEmailSetupData.name.trim() ||
            emaiSetupDetails.emailAddress.trim() !== originalEmailSetupData.emailAddress.trim() ||
            emaiSetupDetails.application !== originalEmailSetupData.application ||
            emaiSetupDetails.hostId !== originalEmailSetupData.hostId ||
            emaiSetupDetails.emailServerEnabled !== originalEmailSetupData.emailServerEnabled ||
            emaiSetupDetails.startHour !== originalEmailSetupData.startHour ||
            emaiSetupDetails.endHour !== originalEmailSetupData.endHour ||
            emaiSetupDetails.emailServer !== originalEmailSetupData.emailServer ||
            JSON.stringify(emaiSetupDetails.days) !== JSON.stringify(originalEmailSetupData.days)
        );

    };

    /**
     * Determines if the add button should be enabled based on required fields and validation.
     * @returns {boolean} True if add is allowed, false otherwise.
     */
    const isAddEnabled = () => {
        if (originalEmailSetupData) return false;
        if (
            emaiSetupDetails.name.trim() === '' ||
            emaiSetupDetails.emailAddress.trim() === '' ||
            emaiSetupDetails.application === '' ||
            emaiSetupDetails.emailServer === '' ||
            emaiSetupDetails.startHour === '' ||
            emaiSetupDetails.endHour === '' ||
            !Object.values(emaiSetupDetails.days).some(Boolean)
        ) {
            return false;
        }
        if (Number(emaiSetupDetails.endHour) < Number(emaiSetupDetails.startHour)) {
            return false;
        }

        return true;
    };

    /**
     * Handles the save action for both add and update operations, sending the payload to the API.
     */
    const handleSave = async () => {
        const payload = {
            name: emaiSetupDetails.name.trim(),
            emailAddress: emaiSetupDetails.emailAddress.trim(),
            application: emaiSetupDetails.application.trim(),
            startHour: Number(emaiSetupDetails.startHour),
            endHour: Number(emaiSetupDetails.endHour),
            hostId: emaiSetupDetails.hostId,
            activeInactive: emaiSetupDetails.activeInactive ? 'Y' : 'N',
            days: Object.keys(emaiSetupDetails.days).filter(day => emaiSetupDetails.days[day]).join(','),
        };
        try {
            let response;
            if (selectedEditRecords && Object.keys(selectedEditRecords).length > 0) {
                response = await emailSetupService.updateEmailSetup(payload,emaiSetupDetails.aspId);
                onSuccess('update');
            } else {
                response = await emailSetupService.addEmailSetup(payload);
                onSuccess('add');
            }
        } catch (error) {
        }
    };


    return (
        <>
            <Dialog open={open} onClose={handleClose} PaperProps={{ className: 'email-setup-dialog' }}>
                <DialogTitle>Email Setup</DialogTitle>
                <IconButton
                    aria-label="close"
                    onClick={handleClose}
                >
                    <CloseIcon />
                </IconButton>
                <DialogContent dividers>
                    <div className='email-setup-dialog-content'>
                        <div className='dialog-content-details'>
                            <div className='name-details email-details'>
                                <span className='label-text'>Name</span>
                                <CFormInput
                                    className='input-textbox email-text'
                                    name='name'
                                    maxLength={25}
                                    value={emaiSetupDetails.name}
                                    onChange={handleChange}
                                />
                            </div>
                            <div className='name-details email-details'>
                                <span className='label-text'>Email Address</span>
                                <CFormInput
                                    className='input-textbox email-text'
                                    name='emailAddress'
                                    maxLength={50}
                                    value={emaiSetupDetails.emailAddress}
                                    onChange={handleChange}
                                />
                            </div>
                            <div className='name-details email-details'>
                                <span className='label-text'>Application</span>
                                <CFormSelect
                                    className='input-textbox email-text'
                                    name='application'
                                    value={emaiSetupDetails.application}
                                    onChange={handleChange}
                                >
                                    <option value=''></option>
                                    {
                                        applicationDropdownDetails.map((application) => (
                                            <option key={application} value={application}>
                                                {application}
                                            </option>
                                        ))
                                    }
                                </CFormSelect>
                            </div>
                        </div>
                        <div className='dialog-content-details'>
                            <div className='name-details email-details'>
                                <span className='label-text'>Email Server</span>
                                <CFormSelect
                                    className='input-textbox email-text'
                                    name='hostId'
                                    value={emaiSetupDetails.hostId}
                                    onChange={handleChange}
                                >
                                    <option value=''></option>
                                    {
                                        emailServerDropdownDetails.map((server) => (
                                            <option key={server.hostId} value={server.hostId}>
                                                {server.serverDescription} {server.hostId}
                                            </option>
                                        ))
                                    }
                                </CFormSelect>
                            </div>
                            <div className='name-details start-hour'>
                                <span className='label-text'>start Hour</span>
                                <CFormSelect
                                    className='input-textbox start-hour'
                                    name='startHour'
                                    value={emaiSetupDetails.startHour}
                                    onChange={handleChange}
                                >
                                    <option value=""></option>
                                    {[...Array(24).keys()].map((h) => (
                                        <option key={h} value={h}>{`${h}:00`}</option>
                                    ))}
                                </CFormSelect>
                            </div>
                            <div className='name-details start-hour'>
                                <span className='label-text'>End Hour</span>
                                <CFormSelect
                                    className='input-textbox start-hour'
                                    name='endHour'
                                    value={emaiSetupDetails.endHour}
                                    onChange={handleChange}
                                >
                                    <option value=""></option>
                                    {[...Array(24).keys()].map((h) => (
                                        <option key={h} value={h}>{`${h}:00`}</option>
                                    ))}
                                </CFormSelect>
                            </div>
                            <div className='active-container'>
                                <CFormCheck
                                    className='active-check'
                                    label='Active'
                                    name='activeInactive'
                                    checked={!!emaiSetupDetails.activeInactive}
                                    onChange={handleChange}
                                />
                            </div>
                        </div>
                        <div className='dialog-content-details'>
                            <CFormCheck label="Sunday" name="Su" checked={emaiSetupDetails.days.Su} onChange={handleChange} />
                            <CFormCheck label="Monday" name="M" checked={emaiSetupDetails.days.M} onChange={handleChange} />
                            <CFormCheck label="Tuesday" name="T" checked={emaiSetupDetails.days.T} onChange={handleChange} />
                            <CFormCheck label="Wednesday" name="W" checked={emaiSetupDetails.days.W} onChange={handleChange} />
                            <CFormCheck label="Thursday" name="Th" checked={emaiSetupDetails.days.Th} onChange={handleChange} />
                            <CFormCheck label="Friday" name="F" checked={emaiSetupDetails.days.F} onChange={handleChange} />
                            <CFormCheck label="Saturday" name="S" checked={emaiSetupDetails.days.S} onChange={handleChange} />
                            <Button variant="contained" size="small" onClick={handleSelectAllDays}>ALL</Button>
                        </div>
                    </div>
                </DialogContent>
                <DialogActions>
                    <div className='email-setup-button-container'>
                        <Button variant="outlined" size="small" onClick={handleClose}>Cancel</Button>
                        <Button variant="contained" size="small" disabled={selectedEditRecords && Object.keys(selectedEditRecords).length > 0 ? !isUpdateEnabled() : !isAddEnabled()} onClick={handleSave}>Save</Button>
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



export default EmailSetupDialog;








