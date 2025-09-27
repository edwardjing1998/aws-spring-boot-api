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

// ⬇️ import your local JSON
import { emailSetupData } from './data' // make sure data.js exports `emailSetupData`

ModuleRegistry.registerModules([ClientSideRowModelModule])

const EmailSetup = () => {
  const containerStyle = useMemo(() => ({ width: '100%', height: '543px', paddingTop: '16px' }), [])
  const gridStyle = useMemo(() => ({ height: '100%', width: '100%' }), [])
  const rowSelection = useMemo(() => ({ mode: 'multiRow' }), [])

  const [pageSize, setPageSize] = useState(10)

  const [allRows, setAllRows] = useState([]) // cleaned data here
  const gridApi = useRef(null)
  const [refreshKey, setRefreshKey] = useState(0)
  const [selectedRows, setSelectedRows] = useState([])

  const [isAddEmailSetupDialog, setIsAddEmailSetupDialog] = useState(false)
  const [selectedEditRecord, setSelectedEditRecord] = useState({})
  const [snackbarType, setSnackbarType] = useState('none')
  const [totalCount, setTotalCount] = useState(0)

  // Clean & load local JSON once
  useEffect(() => {
    const list = emailSetupData?.response?.listEmails ?? []

    // helper to trim strings safely and remove odd leading quotes
    const clean = (v) => (typeof v === 'string' ? v.trim().replace(/^"+/, '') : v)

    const rows = list.map((item, idx) => ({
      aspId: idx + 1, // synthetic ID (since not in JSON)
      activeInactive: clean(item.activeInactive),
      name: clean(item.name),
      emailAddress: clean(item.emailAddress),
      application: clean(item.application),
      startHour: Number(item.startHour ?? 0),
      endHour: Number(item.endHour ?? 0),
      days: clean(item.days), // WeekDaysRenderer can keep using the string
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

  // Handlers (kept from your original)
  const handleEditClick = (rowData) => {
    setIsAddEmailSetupDialog(true)
    setSelectedEditRecord(rowData)
  }

  const handleDeleteClick = async (rowData) => {
    setSelectedRows((prev) => [...prev, rowData])
    setSnackbarType('delete')
  }

  const onGridReady = (params) => {
    gridApi.current = params.api
  }

  const showAddDialog = () => {
    setSelectedEditRecord({})
    setIsAddEmailSetupDialog(true)
  }

  const handleDialogSuccess = async (type) => {
    setIsAddEmailSetupDialog(false)
    setSnackbarType(type)
    setRefreshKey((prev) => prev + 1)
    // If you later wire add/update locally, also update `allRows` + `totalCount`
  }

  const handleSnackbarCancel = () => {
    setSelectedEditRecord({})
    setSelectedRows([])
    setSnackbarType('none')
  }

  const handleSnackbarOk = async () => {
    // Since we’re working off local JSON now, we’ll just simulate delete:
    const idsToDelete = selectedRows.map((r) => r.aspId)
    setAllRows((prev) => prev.filter((r) => !idsToDelete.includes(r.aspId)))
    setTotalCount((prev) => Math.max(0, prev - idsToDelete.length))

    setSelectedRows([])
    setSnackbarType('delete-confirmation')
    setRefreshKey((p) => p + 1)
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
            key={pageSize + '_' + refreshKey}
            columnDefs={columnDefs}
            rowData={allRows}                 // ⬅️ client-side rows
            defaultColDef={defaultColDef}
            rowSelection={rowSelection}
            pagination={true}
            paginationPageSize={pageSize}
            paginationPageSizeSelector={[10, 20, 50, 100]}
            context={{ handleEditClick, handleDeleteClick }}
            getRowId={({ data }) => String(data.aspId)}
            onGridReady={onGridReady}
            onSelectionChanged={(params) => setSelectedRows(params.api.getSelectedRows())}
            onPaginationChanged={(params) => {
              const newPageSize = params.api.paginationGetPageSize()
              setPageSize((prev) => (prev !== newPageSize ? newPageSize : prev))
            }}
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
