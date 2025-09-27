// File: src/views/rapid-admin-edit/daily-activity/ZipCodeConfig.jsx
import React, { useEffect, useMemo, useState, useRef } from 'react'
import { CCard, CCardBody } from '@coreui/react'
import { AllCommunityModule, ModuleRegistry } from 'ag-grid-community'
import { AgGridReact } from 'ag-grid-react'
import { Button } from '@mui/material'                      // ⬅️ add

import './ZipCodeConfig.scss'
import { zipCodeTable, usStates } from './data.js'
import ActionCell from './ActionCell.js'
import ZipcodeTableDialog from './ZipCodeEditDialog.js'

ModuleRegistry.registerModules([AllCommunityModule])

const ZipcodeConfig = () => {
  const [rowData, setRowData] = useState([])
  const [selectedKey, setSelectedKey] = useState(null)
  const gridApiRef = useRef(null)

  // dialog state
  const [zipDialogOpen, setZipDialogOpen] = useState(false)
  const [zipDialogInitial, setZipDialogInitial] = useState(null)

  // Build map "TX" -> "Texas"
  const stateMap = useMemo(() => {
    const map = new Map()
    usStates.forEach(({ acronym, stateName }) => map.set(acronym.toUpperCase(), stateName))
    return map
  }, [])

  useEffect(() => {
    const allRows = Array.isArray(zipCodeTable) ? zipCodeTable : Object.values(zipCodeTable).flat()
    setRowData(allRows)
  }, [])

  const formatState = (abbr) => {
    if (!abbr) return ''
    const up = String(abbr).toUpperCase().trim()
    const name = stateMap.get(up)
    return name ? `${name} - ${up}` : `Unknown - ${up}`
  }

  // ——— callbacks used by ActionCell & "New" button ———
  const openZipDialogWithRow = (row) => {
    setZipDialogInitial({
      zip: String(row?.zipCode ?? ''),
      city: row?.city ?? '',
      state: row?.state ?? '',
    })
    setZipDialogOpen(true)
  }

  const openZipDialogCreate = () => {
    // empty initial values for a brand-new entry
    setZipDialogInitial({ zip: '', city: '', state: '' })
    setZipDialogOpen(true)
  }

  const onDelete = (row) => {
    alert(`Delete clicked for Zip: ${row.zipCode ?? '(n/a)'} | City: ${row.city ?? '(n/a)'}`)
  }

  const [colDefs] = useState([
    { field: 'zipCode', headerName: 'Zip Code', flex: 20, minWidth: 120 },
    { field: 'city', headerName: 'City', flex: 20, minWidth: 120 },
    {
      headerName: 'State',
      field: 'state',
      flex: 30,
      minWidth: 160,
      valueGetter: (p) => formatState(p.data?.state),
      comparator: (a, b) => a.localeCompare(b),
    },
    {
      headerName: 'Action',
      field: 'action',
      flex: 30,
      minWidth: 200,
      sortable: false,
      filter: false,
      cellClass: ['action-cell', 'ag-cell-center'],
      cellRenderer: ActionCell,
      cellRendererParams: {
        onEdit: openZipDialogWithRow,
        onCreate: openZipDialogCreate,
        onDelete,
      },
    },
  ])

  const defaultColDef = {
    filter: true,
    floatingFilter: false,
    sortable: true,
    resizable: true,
    cellClass: 'ag-cell-center',
    headerClass: 'ag-header-center',
  }

  return (
    <div className="daily-activity-wrapper">
      <CCard>
        <CCardBody>

          {/* Top toolbar with "New" button */}
          <div style={{
            display: 'flex',
            justifyContent: 'space-between',
            alignItems: 'center',
            marginBottom: 10,
            gap: 8,
          }}>
            <div style={{ fontWeight: 600 }}>Zip Code Table</div>
            <Button variant="contained" size="small" onClick={openZipDialogCreate}>
              New
            </Button>
          </div>

          <div style={{ display: 'flex', justifyContent: 'center' }}>
            <div
              className="ag-grid-container ag-theme-quartz custom-compact no-vertical-borders"
              style={{ height: 600, width: '80%' }}
            >
              <AgGridReact
                rowData={rowData}
                columnDefs={colDefs}
                defaultColDef={defaultColDef}
                pagination
                paginationPageSize={15}
                getRowId={({ data }) => String(data.zipCode)}
                rowSelection="single"
                rowClassRules={{
                  'row-selected-persist': (params) =>
                    selectedKey != null && String(params.data?.zipCode) === String(selectedKey),
                }}
                onRowClicked={(e) => setSelectedKey(String(e.data?.zipCode))}
                onGridReady={(e) => { gridApiRef.current = e.api }}
              />
            </div>
          </div>

        </CCardBody>
      </CCard>

      {/* Dialog mounts here; it opens when zipDialogOpen is true */}
      <ZipcodeTableDialog
        open={zipDialogOpen}
        onClose={() => setZipDialogOpen(false)}
        initialData={zipDialogInitial}   // ok if your dialog ignores this prop
      />
    </div>
  )
}

export default ZipcodeConfig
