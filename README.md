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
    <div>
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

      <div style={containerStyle} className="ag-theme-quartz">
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
      <div style={{ display: 'flex', alignItems: 'center', gap: 8, justifyContent: 'center', paddingTop: 8, flexWrap: 'wrap' }}>
        <button className="pager-btn" style={{ border: 'none', outline: 'none', boxShadow: 'none', background: 'transparent' }} onClick={goFirst} disabled={safePage === 0}>⏮</button>
        <button className="pager-btn"   style={{ border: 'none', outline: 'none', boxShadow: 'none', background: 'transparent' }} onClick={goPrev} disabled={safePage === 0}>◀</button>
        <span style={{ fontSize: 13 }}>
          Page <strong>{safePage + 1}</strong> / {totalPages} &nbsp;|&nbsp; Page size
        </span>
        <select
          value={pageSize}
          onChange={(e) => { setPageSize(Number(e.target.value)); setCurrentPage(0) }}
          style={{ height: 28 }}
        >
          {[10, 20, 50, 100].map((n) => <option key={n} value={n}>{n}</option>)}
        </select>
        <button className="pager-btn" style={{ border: 'none', outline: 'none', boxShadow: 'none', background: 'transparent' }} onClick={goNext} disabled={safePage >= totalPages - 1}>▶</button>
        <button className="pager-btn" style={{ border: 'none', outline: 'none', boxShadow: 'none', background: 'transparent' }} onClick={goLast} disabled={safePage >= totalPages - 1}>⏭</button>
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
