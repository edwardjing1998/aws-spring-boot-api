// File: src/views/rapid-admin-edit/email-setup/EmailSetup.jsx
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
import { emailSetupData } from './data' // ensure data.js exports `emailSetupData`

ModuleRegistry.registerModules([ClientSideRowModelModule])

const EmailSetup = () => {
  const containerStyle = useMemo(() => ({ width: '100%', height: '543px', paddingTop: '16px' }), [])
  const gridStyle = useMemo(() => ({ height: '100%', width: '100%' }), [])
  const rowSelection = useMemo(() => ({ mode: 'multiRow' }), [])

  const gridApi = useRef(null)

  // pagination state
  const [pageSize, setPageSize] = useState(10)

  // data & ui state
  const [allRows, setAllRows] = useState([]) // cleaned data
  const [selectedRows, setSelectedRows] = useState([])
  const [isAddEmailSetupDialog, setIsAddEmailSetupDialog] = useState(false)
  const [selectedEditRecord, setSelectedEditRecord] = useState({})
  const [snackbarType, setSnackbarType] = useState('none')
  const [totalCount, setTotalCount] = useState(0)

  // Clean & load local JSON once
  useEffect(() => {
    const list = emailSetupData?.response?.listEmails ?? []

    // trim safely & strip odd leading quotes
    const clean = (v) => (typeof v === 'string' ? v.trim().replace(/^"+/, '') : v)

    const rows = list.map((item, idx) => ({
      aspId: idx + 1, // synthetic ID
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
  }, [])

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

  const defaultColDef = useMemo(
    () => ({
      flex: 1,
      minWidth: 100,
    }),
    [],
  )

  // Handlers
  const handleEditClick = (rowData) => {
    setIsAddEmailSetupDialog(true)
    setSelectedEditRecord(rowData)
  }

  const handleDeleteClick = async (rowData) => {
    setSelectedRows((prev) => {
      // avoid duplicates if user clicks the same row's delete multiple times
      const exists = prev.some((r) => r.aspId === rowData.aspId)
      return exists ? prev : [...prev, rowData]
    })
    setSnackbarType('delete')
  }

  const onGridReady = (params) => {
    gridApi.current = params.api
    // ⚠️ Don't force paginationSetPageSize here; the prop handles it.
  }

  // whenever data changes (e.g., delete), clamp current page to a valid one
  useEffect(() => {
    const api = gridApi.current
    if (!api) return
    const totalPages = api.paginationGetTotalPages?.()
    if (!totalPages || totalPages <= 0) return
    const current = api.paginationGetCurrentPage?.() ?? 0
    if (current >= totalPages) {
      api.paginationGoToLastPage()
    }
  }, [allRows])

  const showAddDialog = () => {
    setSelectedEditRecord({})
    setIsAddEmailSetupDialog(true)
  }

  const handleDialogSuccess = async (type) => {
    setIsAddEmailSetupDialog(false)
    setSnackbarType(type)
    gridApi.current?.paginationGoToFirstPage()
  }

  const handleSnackbarCancel = () => {
    setSelectedEditRecord({})
    setSelectedRows([])
    setSnackbarType('none')
  }

  const handleSnackbarOk = async () => {
    // simulate delete on local JSON
    const idsToDelete = selectedRows.map((r) => r.aspId)
    setAllRows((prev) => prev.filter((r) => !idsToDelete.includes(r.aspId)))
    setTotalCount((prev) => Math.max(0, prev - idsToDelete.length))
    setSelectedRows([])
    setSnackbarType('delete-confirmation')
  }

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
          <span className="text-count">{selectedRows.length} Rows selected</span>
        </div>
      )}

      <div style={containerStyle} className="ag-theme-quartz">
        <div style={gridStyle}>
          <AgGridReact
            key={`${allRows.length}-${pageSize}`}   // ⬅️ ensure a clean mount when data/pageSize changes
            columnDefs={columnDefs}
            rowData={allRows}
            defaultColDef={defaultColDef}
            rowSelection={rowSelection}
            pagination={true}
            paginationPageSize={pageSize}
            // If your AG Grid version supports it, keep this; otherwise remove it:
            // paginationPageSizeSelector={[10, 20, 50, 100]}
            context={{ handleEditClick, handleDeleteClick }}
            getRowId={({ data }) => String(data.aspId)}
            onGridReady={onGridReady}
            onSelectionChanged={(params) => setSelectedRows(params.api.getSelectedRows())}
            // ⛔ removed onPaginationChanged to avoid swallowing the first click
          />
        </div>
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
