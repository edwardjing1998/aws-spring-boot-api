import React, { useState, useEffect, useMemo, useRef } from 'react'
import { CCard, CCardBody } from '@coreui/react'
import { ModuleRegistry } from 'ag-grid-community'
import { ClientSideRowModelModule } from 'ag-grid-community'
import { AgGridReact } from 'ag-grid-react'
import './DailyMessage.scss'
import { clientTransactionData } from './data.js'

import { IconButton } from '@mui/material'
import EditOutlinedIcon from '@mui/icons-material/EditOutlined'
import AddCircleOutlineIcon from '@mui/icons-material/AddCircleOutline'
import DeleteOutlineOutlinedIcon from '@mui/icons-material/DeleteOutlineOutlined'

import DailyMessageDialog from './DailyMessageDialog.js'

// ✅ Community only
ModuleRegistry.registerModules([ClientSideRowModelModule])

const PAGE_SIZE = 25

const DailyActivity = () => {
  const [selectedOption, setSelectedOption] = useState(['ALL'])
  const [fromDate, setFromDate] = useState('')
  const [toDate, setToDate] = useState('')
  const [sourceRows, setSourceRows] = useState([])
  const [expandedGroups, setExpandedGroups] = useState({})

  // pagination (fixed 25/page)
  const [currentPage, setCurrentPage] = useState(0)

  // 🔔 dialog state
  const [dialogOpen, setDialogOpen] = useState(false)
  const [dialogAction, setDialogAction] = useState(null)  // 'edit' | 'add' | 'delete'
  const [dialogRow, setDialogRow] = useState(null)        // store clicked row (optional)

  const gridApiRef = useRef(null)

  useEffect(() => {
    const defaultFrom = '2025-03-01'
    const defaultTo = '2025-04-01'

    setFromDate(defaultFrom)
    setToDate(defaultTo)


    setSelectedOption(['ALL'])
    filterData(['ALL'], defaultFrom, defaultTo)
  }, [])

  const filterData = (clientIds, from, to) => {
    let filtered = []

    if (clientIds.includes('ALL')) {
      filtered = Object.values(clientTransactionData).flat()
    } else {
      clientIds.forEach((cid) => {
        filtered.push(...(clientTransactionData[cid] || []))
      })
    }

    filtered = filtered.map((item) => {
      const transDate =
        String(item?.messageDate ?? '').slice(0, 10) ||
        (item?.dateTime ? String(item.dateTime).slice(0, 10) : '')
      return { ...item, transDate }
    })

    if (from && to) {
      const fromDateObj = new Date(from)
      const toDateObj = new Date(to)
      filtered = filtered.filter((item) => {
        const itemDate = new Date(item.dateTime ?? item.transDate)
        return itemDate >= fromDateObj && itemDate <= toDateObj
      })
    }

    filtered.sort((a, b) => {
      if (a.transDate === b.transDate) {
        return String(a.messageDate).localeCompare(String(b.messageDate))
      }
      return a.transDate.localeCompare(b.transDate)
    })

    setSourceRows(filtered)
    setCurrentPage(0)
  }

  const handlePreview = () => {
    const clientIds = selectedOption.length > 0 ? selectedOption : ['ALL']
    filterData(clientIds, fromDate, toDate)
  }

  const tableData = useMemo(() => {
    const byDate = sourceRows.reduce((m, r) => {
      const d = r.transDate || ''
      ;(m[d] = m[d] || []).push(r)
      return m
    }, {})

    const nextExpanded = { ...expandedGroups }
    Object.keys(byDate).forEach((d) => {
      if (nextExpanded[d] === undefined) nextExpanded[d] = false
    })
    if (Object.keys(nextExpanded).length !== Object.keys(expandedGroups).length) {
      Promise.resolve().then(() => setExpandedGroups(nextExpanded))
    }

    const out = []
    Object.keys(byDate)
      .sort()
      .forEach((d) => {
        const children = byDate[d]
        out.push({
          id: `g-${d}`,
          isGroup: true,
          groupLevel: 1,
          groupLabel: d,
          dateKey: d,
        })
        if (expandedGroups[d]) {
          out.push(
            ...children.map((r, idx) => ({
              ...r,
              id: `${d}-${idx}-${r.messageDate ?? ''}`,
              isGroup: false,
              dateKey: d,
            })),
          )
        }
      })
    return out
  }, [sourceRows, expandedGroups])

  // group-aware pagination using fixed PAGE_SIZE
  const pageRanges = useMemo(() => {
    if (!tableData.length) return [{ start: 0, end: 0 }]
    const ranges = []
    let i = 0
    const n = tableData.length

    const groups = []
    while (i < n) {
      if (tableData[i].isGroup) {
        const start = i
        let j = i + 1
        while (j < n && !tableData[j].isGroup) j++
        groups.push({ start, end: j, size: j - start })
        i = j
      } else {
        groups.push({ start: i, end: i + 1, size: 1 })
        i += 1
      }
    }

    let cursor = 0
    const pushRange = (s, e) => ranges.push({ start: s, end: e })

    while (cursor < groups.length) {
      let remaining = PAGE_SIZE
      let s = groups[cursor].start
      let e = s

      const g = groups[cursor]
      if (g.size > PAGE_SIZE) {
        let splitStart = g.start
        while (splitStart < g.end) {
          const splitEnd = Math.min(splitStart + PAGE_SIZE, g.end)
          pushRange(splitStart, splitEnd)
          splitStart = splitEnd
        }
        cursor += 1
        continue
      }

      while (cursor < groups.length && groups[cursor].size <= remaining) {
        const gg = groups[cursor]
        e = gg.end
        remaining -= gg.size
        cursor += 1
      }
      pushRange(s, e)
    }

    return ranges.length ? ranges : [{ start: 0, end: 0 }]
  }, [tableData])

  const totalPages = pageRanges.length
  const safePage = Math.min(Math.max(currentPage, 0), Math.max(totalPages - 1, 0))

  useEffect(() => {
    if (currentPage !== safePage) setCurrentPage(safePage)
  }, [totalPages]) // eslint-disable-line react-hooks/exhaustive-deps

  const pagedData = useMemo(() => {
    const { start, end } = pageRanges[safePage] || { start: 0, end: 0 }
    return tableData.slice(start, end)
  }, [pageRanges, safePage, tableData])

  // open dialog with context
  const openDialog = (action, row) => {
    setDialogAction(action)
    setDialogRow(row || null)
    setDialogOpen(true)
  }
  const handleDialogClose = () => {
    setDialogOpen(false)
    setDialogAction(null)
    setDialogRow(null)
  }

  const columnDefs = useMemo(
    () => [
      {
        field: 'groupLabel',
        headerName: 'Date',
        colSpan: (params) => (params.data?.isGroup ? 2 : 1),
        cellRenderer: (params) => {
          if (!params.data?.isGroup) return ''
          const d = params.data.groupLabel
          const open = expandedGroups[d] ?? false
          return (
            <div className="group-row" style={{ fontWeight: 300 }}>
              <span style={{ marginRight: 6 }}>{open ? '-' : '+'}</span>
              <span role="img" aria-label="calendar" style={{ marginRight: 6 }}>&nbsp;&nbsp;</span>
              {d}
            </div>
          )
        },
        valueGetter: (params) => (params.data?.isGroup ? params.data.groupLabel : ''),
        flex: 0.6,
        minWidth: 160,
        suppressMovable: true,
      },
      {
        field: 'messageDate',
        headerName: 'Message Date',
        minWidth: 120,
        flex: 1.2,
        valueGetter: (p) => (p.data?.isGroup ? '' : p.data?.messageDate ?? ''),
        cellRenderer: (p) => {
          if (p.data?.isGroup) return ''
          return (
            <span>
              <span role="img" aria-label="hash" style={{ marginRight: 6 }}>#</span>
              {p.value}
            </span>
          )
        },
      },
      {
        field: 'messageText',
        headerName: 'Message Text',
        minWidth: 240,
        flex: 2,
        valueGetter: (p) => (p.data?.isGroup ? '' : p.data?.messageText ?? ''),
        cellRenderer: (p) => {
          if (p.data?.isGroup) return ''
          const messageText = p.data?.messageText ?? ''
          return <span className="case-text">{messageText}</span>
        },
      },
      {
        field: 'action',
        headerName: 'Action',
        minWidth: 160,
        flex: 0,
        sortable: false,
        filter: false,
        cellRenderer: (p) => {
          if (p.data?.isGroup) return ''
          const row = p.data
          const messageText = row?.messageText ?? ''
          const messageDate = row?.messageDate ?? ''

          const onEdit = (e) => { e.stopPropagation(); openDialog('edit', row) }
          const onCreate = (e) => { e.stopPropagation(); openDialog('add', row) }
          const onDelete = (e) => { e.stopPropagation(); openDialog('delete', row) }

          return (
            <div style={{ display: 'flex', gap: 8, alignItems: 'center', justifyContent: 'flex-start' }}>
              <IconButton size="small" onClick={onEdit}   sx={{ color: 'gray', p: 0.25 }} title={`Edit: ${messageText} (${messageDate})`}>
                <EditOutlinedIcon fontSize="inherit" />
              </IconButton>
              <IconButton size="small" onClick={onCreate} sx={{ color: 'gray', p: 0.25 }} title={`Add for: ${messageText} (${messageDate})`}>
                <AddCircleOutlineIcon fontSize="inherit" />
              </IconButton>
              <IconButton size="small" onClick={onDelete} sx={{ color: 'gray', p: 0.25 }} title={`Delete: ${messageText} (${messageDate})`}>
                <DeleteOutlineOutlinedIcon fontSize="inherit" />
              </IconButton>
            </div>
          )
        },
      },
    ],
    [expandedGroups],
  )

  const defaultColDef = {
    flex: 1,
    resizable: true,
    minWidth: 120,
    sortable: false,
    filter: false,
    floatingFilter: false,
  }

  const rowClassRules = {
    'client-group-row': (params) => params.data?.isGroup && params.data?.groupLevel === 1,
  }

    const onRowClicked = (event) => {
    const row = event.data;
    if (row?.isGroup && row?.dateKey) {
      // 1) Clear focused cell so the grid won't try to restore it during your data change
      gridApiRef.current?.clearFocusedCell();
  
      // 2) Defer state change to the next macrotask so it won't run mid-render
      setTimeout(() => {
        setExpandedGroups(prev => ({
          ...prev,
          [row.dateKey]: !(prev[row.dateKey] ?? false),
        }));
      }, 0);
    }
  };

  const goFirst = () => setCurrentPage(0)
  const goPrev = () => setCurrentPage((p) => Math.max(0, p - 1))
  const goNext = () => setCurrentPage((p) => Math.min(totalPages - 1, p + 1))
  const goLast = () => setCurrentPage(Math.max(0, totalPages - 1))

  return (
    <div className="daily-activity-wrapper">
      <CCard>
        <CCardBody>

          <div
            className="ag-grid-container ag-theme-quartz no-grid-border"
            style={{
              height: '600px',
              '--ag-font-size': '12px',
              '--ag-row-height': '28px',
              '--ag-header-height': '30px',
              '--ag-grid-size': '2px',
            }}
          >
            <AgGridReact
              rowData={pagedData}
              columnDefs={columnDefs}
              defaultColDef={defaultColDef}
              rowClassRules={rowClassRules}
              pagination={false}
              suppressScrollOnNewData={true}
              onGridReady={(params) => { gridApiRef.current = params.api }}
              animateRows={true}
              onRowClicked={onRowClicked}
            />
          </div>

          <div
            style={{
              paddingTop: 6,
              display: 'flex',
              alignItems: 'center',
              justifyContent: 'center',
              gap: 8,
              flexWrap: 'wrap',
            }}
          >
            <button className="pager-btn" onClick={goFirst} disabled={safePage === 0}>⏮</button>
            <button className="pager-btn" onClick={goPrev}  disabled={safePage === 0}>◀</button>
            <span style={{ padding: '0 6px', fontSize: '13px' }}>
              Page <strong>{safePage + 1}</strong> / {Math.max(totalPages, 1)} (25 per page)
            </span>
            <button className="pager-btn" onClick={goNext} disabled={safePage >= totalPages - 1}>▶</button>
            <button className="pager-btn" onClick={goLast} disabled={safePage >= totalPages - 1}>⏭</button>
          </div>

          {/* Mount the dialog; extra props (action/row) are kept for future use */}
          <DailyMessageDialog
            key={`${dialogAction || 'none'}-${dialogRow?.messageDate || 'na'}-${dialogOpen}`}
            open={dialogOpen}
            onClose={handleDialogClose}
            action={dialogAction}
            row={dialogRow}
          />
        </CCardBody>
      </CCard>
    </div>
  )
}

export default DailyActivity
