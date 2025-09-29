import React, { useState, useEffect, useMemo, useRef } from 'react'
import { CCard, CCardBody } from '@coreui/react'
import { ModuleRegistry } from 'ag-grid-community'
import { ClientSideRowModelModule } from 'ag-grid-community'
import { AgGridReact } from 'ag-grid-react'
import './DailyMessage.scss'
import { clientTransactionData } from './data.js'

import { IconButton, Button } from '@mui/material'
import EditOutlinedIcon from '@mui/icons-material/EditOutlined'
import AddCircleOutlineIcon from '@mui/icons-material/AddCircleOutline'
import DeleteOutlineOutlinedIcon from '@mui/icons-material/DeleteOutlineOutlined'

import DailyMessageDialog from './DailyMessageDialog.js'

// ✅ Community only
ModuleRegistry.registerModules([ClientSideRowModelModule])

const PAGE_SIZE = 15

const DailyActivity = () => {
  const [selectedOption, setSelectedOption] = useState(['ALL'])
  const [fromDate, setFromDate] = useState('')
  const [toDate, setToDate] = useState('')
  const [sourceRows, setSourceRows] = useState([])
  const [expandedGroups, setExpandedGroups] = useState({})
  const [currentPage, setCurrentPage] = useState(0)

  const [dialogOpen, setDialogOpen] = useState(false)
  const [dialogAction, setDialogAction] = useState(null)
  const [dialogRow, setDialogRow] = useState(null)

  const gridApiRef = useRef(null)

  useEffect(() => {
    const defaultFrom = '2025-09-01'
    const defaultTo = '2025-09-30'
    setFromDate(defaultFrom)
    setToDate(defaultTo)
    setSelectedOption(['ALL'])
    filterData(['ALL'], defaultFrom, defaultTo)
  }, [])

  const toDay = (val) => {
    if (!val) return ''
    if (typeof val === 'string') return val.slice(0, 10)
    try { return new Date(val).toISOString().slice(0, 10) } catch { return '' }
  }

  const filterData = (clientIds, from, to) => {
    let f = toDay(from)
    let t = toDay(to)
    if (f && t && f > t) {
      const tmp = f; f = t; t = tmp
      setFromDate(f); setToDate(t)
    }

    let rows = []
    if (clientIds.includes('ALL')) {
      rows = Object.values(clientTransactionData).flat()
    } else {
      clientIds.forEach((cid) => rows.push(...(clientTransactionData[cid] || [])))
    }

    const withDay = rows.map((item) => {
      const messageDay = toDay(item?.messageDate)
      return { ...item, transDate: messageDay }
    })

    let filtered = withDay
    if (f && t) {
      filtered = withDay.filter((r) => {
        const d = r.transDate || ''
        return d && d >= f && d <= t
      })
    }

    filtered.sort((a, b) => {
      if (a.transDate === b.transDate) {
        return String(a.messageDate ?? '').localeCompare(String(b.messageDate ?? ''))
      }
      return String(a.transDate ?? '').localeCompare(String(b.transDate ?? ''))
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

  // ---- paging (define ONCE)
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
    const row = event.data
    if (row?.isGroup && row?.dateKey) {
      gridApiRef.current?.clearFocusedCell()
      setTimeout(() => {
        setExpandedGroups((prev) => ({
          ...prev,
          [row.dateKey]: !(prev[row.dateKey] ?? false),
        }))
      }, 0)
    }
  }

  const goFirst = () => setCurrentPage(0)
  const goPrev  = () => setCurrentPage((p) => Math.max(0, p - 1))
  const goNext  = () => setCurrentPage((p) => Math.min(totalPages - 1, p + 1))
  const goLast  = () => setCurrentPage(Math.max(0, totalPages - 1))

  return (
    <div className="daily-activity-wrapper">
      <CCard>
        <CCardBody>

          {/* Toolbar */}
          <div
            style={{
              display: 'flex',
              alignItems: 'center',
              justifyContent: 'space-between',
              gap: 12,
              marginBottom: 30,
              flexWrap: 'wrap',
            }}
          >
            <div style={{ display: 'flex', alignItems: 'center', gap: 8, flexWrap: 'wrap' }}>
              <label style={{ fontSize: 13 }}>From</label>
              <input
                type="date"
                value={fromDate}
                onChange={(e) => setFromDate(e.target.value)}
                style={{ height: 30, padding: '0 8px' }}
              />
              <label style={{ fontSize: 13, marginLeft: 6 }}>To</label>
              <input
                type="date"
                value={toDate}
                onChange={(e) => setToDate(e.target.value)}
                style={{ height: 30, padding: '0 8px' }}
              />
              <Button variant="outlined" size="small" onClick={handlePreview}>
                Confirm
              </Button>
            </div>

            <div>
              <Button variant="contained" size="small" onClick={() => openDialog('add', null)}>
                New
              </Button>
            </div>
          </div>

          <div
            className="ag-grid-container ag-theme-quartz no-grid-border"
            style={{
              height: '600px',
              '--ag-font-size': '12px',
              '--ag-row-height': '28px',
              '--ag-header-height': '30px',
              '--ag-grid-size': '2px',
              '--ag-header-background-color': '#DAEDF0',
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
              Page <strong>{safePage + 1}</strong> / {Math.max(totalPages, 1)} ({PAGE_SIZE} per page)
            </span>
            <button className="pager-btn" onClick={goNext} disabled={safePage >= totalPages - 1}>▶</button>
            <button className="pager-btn" onClick={goLast} disabled={safePage >= totalPages - 1}>⏭</button>
          </div>

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



import React, { useRef, useState, useMemo, useEffect } from 'react';
import { Dialog, DialogTitle, DialogContent, DialogActions, Button } from '@mui/material';
import * as dailyMessageService from '../../../services/AdminEditService/DailyMessageService';
import CustomSnackbar from '../../../components/CustomSnackbar';
import { CloseIcon, DailyMessage } from '../../../assets/brand/svg-constants';
import { CFormInput, CFormTextarea } from '@coreui/react';
import IconButton from '@mui/material/IconButton';
import '../../../scss/DailyMessage.scss';

const DailyMessageDialog = ({ open, onClose, action, row }) => {
  // YYYY-MM-DD helper
  const toYMD = (dLike) => {
    if (!dLike) return new Date().toISOString().slice(0, 10);
    try {
      // dLike might already be 'YYYY-MM-DD' or an ISO string
      const s = String(dLike);
      if (s.length >= 10 && s[4] === '-' && s[7] === '-') return s.slice(0, 10);
      return new Date(dLike).toISOString().slice(0, 10);
    } catch {
      return new Date().toISOString().slice(0, 10);
    }
  };

  const [selectedDate, setSelectedDate] = useState(() => new Date().toISOString().slice(0, 10));
  const [messageText, setMessageText] = useState('');
  const [originalMessageText, setOriginalMessageText] = useState('');
  const [snackbarType, setSnackbarType] = useState('none');

  const resetToToday = () => {
    setSelectedDate(new Date().toISOString().slice(0, 10));
    setMessageText('');
    setOriginalMessageText('');
  };

  const handleClose = (event, reason) => {
    if (reason === 'backdropClick' || reason === 'escapeKeyDown') return;
    setSnackbarType('none');
    resetToToday();
    onClose();
  };

  // fetch daily message by date
  const getDailyMessageDetails = async (ymd) => {
    try {
      const dateIso = `${ymd}T00:00:00`;
      const response = await dailyMessageService.fetchDailyMessageDetails(dateIso);
      const msg = response?.[0]?.messageText ?? '';
      setMessageText(msg);
      setOriginalMessageText(msg);
    } catch (error) {
      // if nothing found or error -> clear
      setMessageText('');
      setOriginalMessageText('');
    }
  };

  // When dialog opens, hydrate from clicked row (if provided), else fetch by current selectedDate
  useEffect(() => {
    if (!open) return;

    if (row && !row.isGroup) {
      const ymd = toYMD(row.messageDate || row.transDate);
      setSelectedDate(ymd);

      // If row already has messageText, use it. Otherwise fetch by date.
      if (typeof row.messageText === 'string') {
        setMessageText(row.messageText);
        setOriginalMessageText(row.messageText);
      } else {
        getDailyMessageDetails(ymd);
      }
    } else {
      // No row (e.g. "Add"), load whatever is stored for the currently selected date
      getDailyMessageDetails(selectedDate);
    }
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, [open, row, action]);

  // If user changes date manually, (re)fetch for that date
  useEffect(() => {
    if (!open) return;
    if (!row || action !== 'edit') {
      // For "add" or no specific row, changing the date should show that date's current content (if any)
      getDailyMessageDetails(selectedDate);
    }
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, [selectedDate]);

  const onSave = async () => {
    try {
      const dateIso = `${selectedDate}T00:00:00`;
      const reqData = { messageText, messageDate: dateIso };

      if (!originalMessageText) {
        await dailyMessageService.addDailyMessage(reqData);
        setSnackbarType('add');
      } else {
        await dailyMessageService.updateDailyMessage(reqData);
        setSnackbarType('update');
      }
      setOriginalMessageText(messageText);
    } catch (error) {
      // you could set a 'info'/'error' snackbar if you want
    }
  };

  const handleSnackbarCancel = () => setSnackbarType('none');

  const dialogLabel =
    action === 'edit' ? 'Edit Message' :
    action === 'add' ? 'Add Message' :
    action === 'delete' ? 'Delete Message' :
    'Daily Message';

  return (
    <>
      <Dialog open={open} onClose={handleClose} PaperProps={{ className: 'daily-message-dialog' }}>
        <DialogTitle>
          <div className="daily-message-icon"><DailyMessage /></div>
          {dialogLabel}
        </DialogTitle>
        <IconButton aria-label="close" onClick={handleClose}>
          <CloseIcon />
        </IconButton>

        <DialogContent dividers>
          <div className="date-layout">
            <span className='date-text'>Date</span>
            <CFormInput
              type="date"
              className='date-input'
              value={selectedDate}
              onChange={e => {
                const ymd = e.target.value;
                setSelectedDate(ymd);
                // Clear while fetching
                setMessageText('');
                setOriginalMessageText('');
              }}
            />
          </div>

          <div>
            <CFormTextarea
              className='message-input'
              value={messageText}
              onChange={e => setMessageText(e.target.value)}
              maxLength={250}
            />
            {messageText.length > 0 ? (
              <span className="count-text">
                {' '}Character count - <span className='count'>{messageText.length} / 250</span>
              </span>
            ) : null}
          </div>
        </DialogContent>

        <DialogActions>
          <div className='daily-message-button-container'>
            <Button variant="outlined" size="small" onClick={handleClose}>Cancel</Button>
            <Button
              variant="contained"
              size="small"
              disabled={!messageText || messageText === originalMessageText}
              onClick={onSave}
            >
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
        title={(snackbarType === 'update' || snackbarType === 'add') ? 'Daily Message' : ''}
        body={
          snackbarType === 'update' ? 'You have successfully Updated a Message Text' :
          snackbarType === 'add'    ? 'You have successfully Added a Message Text'   : ''
        }
      />
    </>
  );
};

export default DailyMessageDialog;




