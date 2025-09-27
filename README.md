import React, { useEffect, useState } from 'react'
import { Dialog, DialogTitle, DialogContent, DialogActions, Button, IconButton } from '@mui/material'
import { CFormTextarea, CFormSelect } from '@coreui/react'
import { CloseIcon } from '../../../assets/brand/svg-constants'
import * as emailEventIdService from '../../../services/AdminReportService/EmailEventIdService'
import CustomSnackbar from '../../../components/CustomSnackbar'
import '../../../scss/EmailEventId.scss'

/**
 * Props:
 * - open: boolean
 * - onClose: () => void
 * - selectedRow: object | null  (when editing, pass the row; when adding, pass null/undefined)
 * - onSuccess: (type: 'add'|'update') => void
 */
const EmailEventIdDialog = ({ open, onClose, selectedRow, onSuccess }) => {
  const [applicationDropdownOptions, setApplicationDropdownOptions] = useState([])
  const [eventIdDropdownOptions, setEventIdDropdownOptions] = useState([])

  const [data, setData] = useState({
    eventId: '',
    applicationId: '',
    eventTxt: '',
  })
  const [originalData, setOriginalData] = useState(null)
  const [snackbarType, setSnackbarType] = useState('none')

  useEffect(() => {
    if (!open) return

    const init = async () => {
      try {
        const [apps, evts] = await Promise.all([
          emailEventIdService.fetchApplicationIdList(),
          emailEventIdService.fetchEventIdList(),
        ])
        setApplicationDropdownOptions(apps?.response?.ApplicationIdList || [])
        setEventIdDropdownOptions(evts?.response?.EventIdList || [])
      } catch (e) {
        // swallow
      }

      if (selectedRow && Object.keys(selectedRow).length) {
        setData({
          eventId: String(selectedRow.eventId ?? ''),
          applicationId: String(selectedRow.applicationId ?? ''),
          eventTxt: String(selectedRow.eventTxt ?? ''),
        })
        setOriginalData({
          eventId: String(selectedRow.eventId ?? ''),
          applicationId: String(selectedRow.applicationId ?? ''),
          eventTxt: String(selectedRow.eventTxt ?? ''),
        })
      } else {
        setData({ eventId: '', applicationId: '', eventTxt: '' })
        setOriginalData(null)
      }
    }

    init()
  }, [open, selectedRow])

  const handleClose = (event, reason) => {
    if (reason === 'backdropClick' || reason === 'escapeKeyDown') return
    onClose?.()
  }

  const isSaveDisabled = () => {
    const trimmed = {
      eventId: data.eventId.trim(),
      applicationId: data.applicationId.trim(),
      eventTxt: data.eventTxt.trim(),
    }

    // Require all three fields for both add & update
    if (!trimmed.eventId || !trimmed.applicationId || !trimmed.eventTxt) return true

    // If editing, disable when no changes
    if (originalData) {
      return (
        trimmed.eventId === String(originalData.eventId).trim() &&
        trimmed.applicationId === String(originalData.applicationId).trim() &&
        trimmed.eventTxt === String(originalData.eventTxt).trim()
      )
    }
    return false
  }

  const handleSave = async () => {
    const payload = {
      applicationId: data.applicationId.trim(),
      eventId: data.eventId.trim(),
      eventTxt: data.eventTxt.trim(),
    }

    try {
      if (originalData) {
        await emailEventIdService.updateEmailEventId(payload)
        onSuccess?.('update')
      } else {
        await emailEventIdService.initiateEmailEventId(payload)
        onSuccess?.('add')
      }
    } catch (e) {
      setSnackbarType('error')
    }
  }

  const handleSnackbarCancel = () => setSnackbarType('none')

  return (
    <>
      <Dialog open={open} onClose={handleClose} PaperProps={{ className: 'email-event-id-dialog' }}>
        <DialogTitle>Application Event Id</DialogTitle>
        <IconButton aria-label="close" onClick={handleClose}>
          <CloseIcon />
        </IconButton>

        <DialogContent dividers>
          <div className="add-main-container">
            <div className="row-container">
              <div className="eventId-container eventId-field">
                <span className="label">Event ID</span>
                <CFormSelect
                  className="eventId-input applicationId-input"
                  value={data.eventId}
                  onChange={(e) => setData((p) => ({ ...p, eventId: e.target.value }))}
                  aria-label="Event ID"
                >
                  <option value=""></option>
                  {eventIdDropdownOptions.map((opt) => (
                    <option key={opt} value={opt}>
                      {opt}
                    </option>
                  ))}
                </CFormSelect>
              </div>

              <div className="eventId-container eventId-field">
                <span className="label">Application</span>
                <CFormSelect
                  className="eventId-input applicationId-input"
                  value={data.applicationId}
                  onChange={(e) => setData((p) => ({ ...p, applicationId: e.target.value }))}
                  aria-label="Application"
                >
                  <option value=""></option>
                  {applicationDropdownOptions.map((opt) => (
                    <option key={opt} value={opt}>
                      {opt}
                    </option>
                  ))}
                </CFormSelect>
              </div>
            </div>

            <div className="row-container">
              <div className="description-container eventId-field">
                <span className="label">Description</span>
                <CFormTextarea
                  className="eventId-input description-input"
                  value={data.eventTxt}
                  onChange={(e) => setData((p) => ({ ...p, eventTxt: e.target.value }))}
                />
              </div>
            </div>
          </div>
        </DialogContent>

        <DialogActions className="add-action-container">
          <div className="email-event-id-button-container">
            <Button variant="outlined" size="small" onClick={handleClose}>
              Cancel
            </Button>
            <Button variant="contained" size="small" onClick={handleSave} disabled={isSaveDisabled()}>
              Save
            </Button>
          </div>
        </DialogActions>
      </Dialog>

      <CustomSnackbar
        type={snackbarType}
        open={snackbarType !== 'none'}
        handleOk={handleSnackbarCancel}
        onClose={handleSnackbarCancel}
        title={snackbarType === 'error' ? 'Error' : ''}
        body={snackbarType === 'error' ? 'Something went wrong. Please try again.' : ''}
      />
    </>
  )
}

export default EmailEventIdDialog




import React, { useState, useMemo, useRef, useEffect } from 'react'
import { Dialog, DialogTitle, DialogContent, DialogActions, Button } from '@mui/material'
import IconButton from '@mui/material/IconButton'
import { CloseIcon, EditIcon, ClientMappingDeleteIcon, SearchIcon, ApplicationEventId } from '../../../assets/brand/svg-constants'
import { CFormInput } from '@coreui/react'
import * as emailEventIdService from '../../../services/AdminReportService/EmailEventIdService'
import { AgGridReact } from 'ag-grid-react'
import { ModuleRegistry, AllCommunityModule } from 'ag-grid-community'
import CustomSnackbar from '../../../components/CustomSnackbar'
import EmailEventIdDialog from './EmailEventIdDialog'   // ⬅️ NEW
import '../../../scss/EmailEventId.scss'

ModuleRegistry.registerModules([AllCommunityModule])

const EmailEventId = ({ open, onClose }) => {
  const containerStyle = useMemo(() => ({ width: '100%', height: '100%' }), [])
  const gridStyle = useMemo(() => ({ height: '100%', width: '100%' }), [])

  const [columnDefs] = useState([
    { field: 'eventId', headerName: 'Event ID', maxWidth: 100 },
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
  const rowSelection = useMemo(() => ({ mode: 'multiRow' }), [])

  const [selectedRows, setSelectedRows] = useState([])
  const [deleteSnackbarOpen, setDeleteSnackbarOpen] = useState(false)
  const [snackbarType, setSnackbarType] = useState('none')
  const [selectedRowToDelete, setSelectedRowToDelete] = useState(null)
  const [pageSize, setPageSize] = useState(10)
  const gridApi = useRef(null)

  // rows cache for client-side ops (optional)
  const [allRows, setAllRows] = useState([])

  // NEW: small dialog state for Add/Edit
  const [editDialogOpen, setEditDialogOpen] = useState(false)
  const [editDialogRow, setEditDialogRow] = useState(null) // null => Add; object => Edit

  const createDataSource = (pageSizeArg) => ({
    getRows: (params) => {
      const page = params.startRow / pageSizeArg
      emailEventIdService
        .fetchEmailEventIds(page, pageSizeArg)
        .then((data) => {
          const response = data.response.EventIdList
          let rows, lastRow
          if (response && Array.isArray(response.rows) && typeof response.totalElements === 'number') {
            rows = response.rows
            lastRow = response.totalElements
          } else if (Array.isArray(response) && typeof response.totalElements === 'number') {
            rows = response
            lastRow = response.totalElements
          } else if (Array.isArray(response)) {
            rows = response
            lastRow = rows.length < pageSizeArg ? params.startRow + rows.length : -1
          } else {
            rows = []
            lastRow = 0
          }
          setAllRows(rows)
          params.successCallback(rows, lastRow)
        })
        .catch(() => {
          params.failCallback()
        })
    },
  })

  const dataSource = useMemo(() => createDataSource(pageSize), [pageSize])

  const handleAddClick = () => {
    setEditDialogRow(null)      // add mode
    setEditDialogOpen(true)
  }

  const handleEditClick = (rowData) => {
    setEditDialogRow(rowData)   // edit mode
    setEditDialogOpen(true)
  }

  const handleDeleteClick = (rowData) => {
    setSelectedRowToDelete(rowData)
    setDeleteSnackbarOpen(true)
  }

  const onHandleDelete = async () => {
    setDeleteSnackbarOpen(false)
    if (selectedRowToDelete?.eventId) {
      try {
        await emailEventIdService.deleteEmailEventId(selectedRowToDelete.eventId, selectedRowToDelete.applicationId)
        setSnackbarType('delete-confirmation')
        gridApi.current?.refreshInfiniteCache()
      } catch (err) {
        // noop
      }
    }
  }

  const handleClose = (event, reason) => {
    if (reason === 'backdropClick' || reason === 'escapeKeyDown') return
    onClose?.()
  }

  const handleSnackbarCancel = () => {
    setDeleteSnackbarOpen(false)
    setSnackbarType('none')
  }

  const onGridReady = (params) => {
    gridApi.current = params.api
  }

  const handleChildSuccess = (type) => {
    setEditDialogOpen(false)
    setEditDialogRow(null)
    setSnackbarType(type) // 'add' or 'update'
    gridApi.current?.refreshInfiniteCache()
  }

  return (
    <>
      <Dialog open={open} onClose={handleClose} PaperProps={{ className: 'email-event-id-dialog' }}>
        <DialogTitle>
          <div className="application-event-id-icon">
            <ApplicationEventId />
          </div>
          Trace Admin Application Event Id
        </DialogTitle>
        <IconButton aria-label="close" onClick={onClose}>
          <CloseIcon />
        </IconButton>

        {/* Top toolbar (search + Add) */}
        <DialogActions>
          <div className="action-container">
            {selectedRows.length > 1 ? (
              <div style={{ display: 'flex', alignItems: 'center', gap: 8 }}>
                <span className="text-count">{selectedRows.length} rows selected</span>
                <Button
                  variant="outlined"
                  size="small"
                  onClick={() => {
                    // multi-delete hook (if you want to support multi)
                    // Collect selectedRows and call your service as needed.
                  }}
                >
                  Delete
                </Button>
              </div>
            ) : (
              <>
                <div className="search-input">
                  <CFormInput placeholder="Search" />
                  <span className="search-icon">
                    <SearchIcon />
                  </span>
                </div>
              </>
            )}
            <div>
              <Button variant="contained" size="small" onClick={handleAddClick}>
                Add
              </Button>
            </div>
          </div>
        </DialogActions>

        <DialogContent>
          <div style={containerStyle}>
            <div style={gridStyle}>
              <AgGridReact
                columnDefs={columnDefs}
                rowModelType="infinite"
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
                onSelectionChanged={(params) => setSelectedRows(params.api.getSelectedRows())}
                onPaginationChanged={(params) => {
                  const newPageSize = params.api.paginationGetPageSize()
                  setPageSize((prev) => (prev !== newPageSize ? newPageSize : prev))
                }}
              />
            </div>
          </div>
        </DialogContent>
      </Dialog>

      {/* Delete confirmation */}
      <CustomSnackbar
        type="delete"
        open={deleteSnackbarOpen}
        onClose={handleSnackbarCancel}
        handleOk={onHandleDelete}
        title="Confirm Delete"
        body="Are you sure you want to delete?"
      />

      {/* Add / Update success */}
      {(snackbarType === 'add' || snackbarType === 'update' || snackbarType === 'delete-confirmation') && (
        <CustomSnackbar
          type={snackbarType}
          open={snackbarType !== 'none'}
          handleOk={handleSnackbarCancel}
          onClose={handleSnackbarCancel}
          title="Client Report Mapping"
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
      )}

      {/* ⬇️ Child dialog for Add/Edit */}
      <EmailEventIdDialog
        open={editDialogOpen}
        onClose={() => {
          setEditDialogOpen(false)
          setEditDialogRow(null)
        }}
        selectedRow={editDialogRow}
        onSuccess={handleChildSuccess}
      />
    </>
  )
}

export default EmailEventId




