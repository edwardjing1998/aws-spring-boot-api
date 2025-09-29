import React, { useState, useEffect, useMemo, useRef } from 'react'

import { ModuleRegistry } from 'ag-grid-community'
import { ClientSideRowModelModule } from 'ag-grid-community'
import { AgGridReact } from 'ag-grid-react'

import TitleContainer from '../../../components/TittleContainer'
import WeekDaysRenderer from './WeekDays'
import EmailSetupDialog from './EmailSetupDialog'
import '../../../scss/EmailSetup.scss'
import CustomSnackbar from '../../../components/CustomSnackbar'
import { EditIcon, ClientMappingDeleteIcon } from '../../../assets/brand/svg-constants'

// ⬇️ local JSON source
import { emailSetupData } from './data'

ModuleRegistry.registerModules([ClientSideRowModelModule])

const EmailSetup = () => {
  const containerStyle = useMemo(() => ({ width: '100%', height: '543px', paddingTop: '16px' }), [])
  const gridStyle = useMemo(() => ({ height: '100%', width: '100%' }), [])
  const rowSelection = useMemo(() => ({ mode: 'multiRow' }), [])
  const gridApi = useRef(null)

  // external pagination state
  const [pageSize, setPageSize] = useState(10)
  const [currentPage, setCurrentPage] = useState(0) // 0-based

  // data & ui state
  const [allRows, setAllRows] = useState([])
  const [selectedRows, setSelectedRows] = useState([])
  const [isAddEmailSetupDialog, setIsAddEmailSetupDialog] = useState(false)
  const [selectedEditRecord, setSelectedEditRecord] = useState({})
  const [snackbarType, setSnackbarType] = useState('none')
  const [totalCount, setTotalCount] = useState(0)

  // Load & normalize JSON once
  useEffect(() => {
    const list = emailSetupData?.response?.listEmails ?? []
    const clean = (v) => (typeof v === 'string' ? v.trim().replace(/^"+/, '') : v)
    const rows = list.map((item, idx) => ({
      aspId: idx + 1,
      activeInactive: clean(item.activeInactive),
      name: clean(item.name),
      emailAddress: clean(item.emailAddress),
      application: clean(item.application),
      startHour: Number(item.startHour ?? 0),
      endHour: Number(item.endHour ?? 0),
      days: clean(item.days),
      hostId: Number(item.hostId ?? 0),
    }))
    setAllRows(rows)
    setTotalCount(rows.length)
    setCurrentPage(0) // reset to first page when data loads
  }, [])

  // derived: total pages & current page slice
  const totalPages = Math.max(1, Math.ceil(allRows.length / pageSize))
  const safePage = Math.min(Math.max(currentPage, 0), totalPages - 1)

  useEffect(() => {
    if (safePage !== currentPage) setCurrentPage(safePage)
  }, [totalPages]) // keep page index valid when data/pageSize changes

  const pagedRows = useMemo(() => {
    const start = safePage * pageSize
    const end = start + pageSize
    return allRows.slice(start, end)
  }, [allRows, safePage, pageSize])

  const [columnDefs] = useState([
    { field: 'aspId', headerName: '#', minWidth: 75 },
    { field: 'activeInactive', headerName: 'Indicator', minWidth: 97 },
    { field: 'name', headerName: 'Name' },
    { field: 'emailAddress', headerName: 'Email' },
    { field: 'application', headerName: 'Application' },
    { field: 'startHour', headerName: 'Start Hr', minWidth: 70 },
    { field: 'endHour', headerName: 'End Hr', minWidth: 70 },
    {
      field: 'days',
      headerName: 'Week Days',
      cellRenderer: WeekDaysRenderer,
      cellClass: 'days-cell',
      minWidth: 150,
    },
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
  ])

  const defaultColDef = useMemo(() => ({ flex: 1, minWidth: 100 }), [])

  // Handlers
  const handleEditClick = (rowData) => {
    setIsAddEmailSetupDialog(true)
    setSelectedEditRecord(rowData)
  }

  const handleDeleteClick = (rowData) => {
    setSelectedRows((prev) => (prev.some((r) => r.aspId === rowData.aspId) ? prev : [...prev, rowData]))
    setSnackbarType('delete')
  }

  const onGridReady = (params) => {
    gridApi.current = params.api
  }

  const showAddDialog = () => {
    setSelectedEditRecord({})
    setIsAddEmailSetupDialog(true)
  }

  const handleDialogSuccess = (type) => {
    setIsAddEmailSetupDialog(false)
    setSnackbarType(type)
    setCurrentPage(0) // go back to first page on add/update
  }

  const handleSnackbarCancel = () => {
    setSelectedEditRecord({})
    setSelectedRows([])
    setSnackbarType('none')
  }

  const handleSnackbarOk = () => {
    const idsToDelete = selectedRows.map((r) => r.aspId)
    const next = allRows.filter((r) => !idsToDelete.includes(r.aspId))
    setAllRows(next)
    setTotalCount(next.length)
    setSelectedRows([])
    setSnackbarType('delete-confirmation')
    // clamp page if we deleted last items on the page
    const nextTotalPages = Math.max(1, Math.ceil(next.length / pageSize))
    if (safePage >= nextTotalPages) setCurrentPage(nextTotalPages - 1)
  }

  // pager controls
  const goFirst = () => setCurrentPage(0)
  const goPrev = () => setCurrentPage((p) => Math.max(0, p - 1))
  const goNext = () => setCurrentPage((p) => Math.min(totalPages - 1, p + 1))
  const goLast = () => setCurrentPage(totalPages - 1)

  return (
    <div className="email-setup-page">
      <TitleContainer
        hideSaveButton={true}
        hideUpdateButton={false}
        onAdd={showAddDialog}
        hideDeleteButton={selectedRows.length > 1 ? true : false}
        selectedDeletedRows={selectedRows}
        onDelete={handleDeleteClick}
      />

      {selectedRows.length > 0 && (
        <div>
          <span className="text-count">{selectedRows.length} Rows selected</span>
        </div>
      )}

      {/* add no-side-borders class to strip L/R borders */}
      <div style={containerStyle} className="ag-theme-quartz no-side-borders">
        <div style={gridStyle}>
          <AgGridReact
            columnDefs={columnDefs}
            rowData={pagedRows}
            defaultColDef={defaultColDef}
            rowSelection={rowSelection}
            pagination={false}
            context={{ handleEditClick, handleDeleteClick }}
            getRowId={({ data }) => String(data.aspId)}
            onGridReady={onGridReady}
            onSelectionChanged={(params) => setSelectedRows(params.api.getSelectedRows())}
          />
        </div>
      </div>

      {/* External pager */}
      <div
        style={{
          display: 'flex',
          alignItems: 'center',
          gap: 8,
          justifyContent: 'center',
          paddingTop: 8,
          flexWrap: 'wrap',
        }}
      >
        <button
          className="pager-btn"
          style={{ border: 'none', outline: 'none', boxShadow: 'none', background: 'transparent' }}
          onClick={goFirst}
          disabled={safePage === 0}
        >
          ⏮
        </button>
        <button
          className="pager-btn"
          style={{ border: 'none', outline: 'none', boxShadow: 'none', background: 'transparent' }}
          onClick={goPrev}
          disabled={safePage === 0}
        >
          ◀
        </button>
        <span style={{ fontSize: 13 }}>
          Page <strong>{safePage + 1}</strong> / {totalPages} &nbsp;|&nbsp; Page size
        </span>
        <select
          value={pageSize}
          onChange={(e) => {
            setPageSize(Number(e.target.value))
            setCurrentPage(0)
          }}
          style={{ height: 28 }}
        >
          {[10, 20, 50, 100].map((n) => (
            <option key={n} value={n}>
              {n}
            </option>
          ))}
        </select>
        <button
          className="pager-btn"
          style={{ border: 'none', outline: 'none', boxShadow: 'none', background: 'transparent' }}
          onClick={goNext}
          disabled={safePage >= totalPages - 1}
        >
          ▶
        </button>
        <button
          className="pager-btn"
          style={{ border: 'none', outline: 'none', boxShadow: 'none', background: 'transparent' }}
          onClick={goLast}
          disabled={safePage >= totalPages - 1}
        >
          ⏭
        </button>
      </div>

      <EmailSetupDialog
        open={isAddEmailSetupDialog}
        onClose={() => setIsAddEmailSetupDialog(false)}
        selectedEditRecords={selectedEditRecord}
        onSuccess={handleDialogSuccess}
      />

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
