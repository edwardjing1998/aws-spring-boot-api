import React, { useState, useEffect, useMemo, useRef } from 'react'
import { CCard, CCardBody } from '@coreui/react'
import { ModuleRegistry } from 'ag-grid-community'
import { ClientSideRowModelModule } from 'ag-grid-community'
import { AgGridReact } from 'ag-grid-react'
import './DailyMessage.scss'
import { clientsJson, clientTransactionData } from './data.js'

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
  const [clientOptions, setClientOptions] = useState([])
  const [sourceRows, setSourceRows] = useState([]) 
  const [expandedGroups, setExpandedGroups] = useState({})

  // 🔢 Pagination state (fixed 25/page)
  const [currentPage, setCurrentPage] = useState(0)

  const gridApiRef = useRef(null)

  useEffect(() => {
    const defaultFrom = '2025-03-01'
    const defaultTo = '2025-04-01'

    setFromDate(defaultFrom)
    setToDate(defaultTo)

    const options = [
      { value: 'ALL', label: 'All Clients' },
      ...clientsJson.map((c) => ({
        value: c.client,
        label: `${c.client} - ${c.name}`,
      })),
    ]
    setClientOptions(options)
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

  // Group-aware pagination using fixed PAGE_SIZE
  const pageRanges = useMemo(() => {
    if (!tableData.length) return [{ start: 0, end: 0 }]
    const ranges = []
    let i = 0
    const n = tableData.length

    // identify groups [start,end)
    const groups = []
    while (i < n) {
      if (tableData[i].isGroup) {
        const start = i
        let j = i + 1
        while (j < n && !tableData[j].isGroup) j++
        groups.push({ start, end: j, size: j - start })
        i = j
      } else {
        // fallback: treat as single-row group
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
        // split large group across multiple pages
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
        minWidth: 220,
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
          if (p.data?.isGroup) return '';
          const messageText = p.data?.messageText ?? '';
          return <span className="case-text">{messageText}</span>;
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
          if (p.data?.isGroup) return '';
          const row = p.data;
          const messageText = row?.messageText ?? '';
          const messageDate = row?.messageDate ?? '';

          const onEdit = (e) => { e.stopPropagation(); alert(`Edit: ${messageText} (date: ${messageDate})`); };
          const onCreate = (e) => { e.stopPropagation(); alert(`Add for: ${messageText} (date: ${messageDate})`); };
          const onDelete = (e) => { e.stopPropagation(); alert(`Delete: ${messageText} (date: ${messageDate})`); };

          return (
            <div style={{ display: 'flex', gap: 8, alignItems: 'center', justifyContent: 'flex-start' }}>
              <IconButton size="small" onClick={onEdit}   sx={{ color: 'gray', p: 0.25 }} title="Edit">
                <EditOutlinedIcon fontSize="inherit" />
              </IconButton>
              <IconButton size="small" onClick={onCreate} sx={{ color: 'gray', p: 0.25 }} title="Add">
                <AddCircleOutlineIcon fontSize="inherit" />
              </IconButton>
              <IconButton size="small" onClick={onDelete} sx={{ color: 'gray', p: 0.25 }} title="Delete">
                <DeleteOutlineOutlinedIcon fontSize="inherit" />
              </IconButton>
            </div>
          );
        },
      }

      
      ,
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
      setExpandedGroups((prev) => ({
        ...prev,
        [row.dateKey]: !(prev[row.dateKey] ?? false),
      }))
      // optional: keep current page; or compute and jump to the page containing this group
    }
  }

  const goFirst = () => setCurrentPage(0)
  const goPrev = () => setCurrentPage((p) => Math.max(0, p - 1))
  const goNext = () => setCurrentPage((p) => Math.min(totalPages - 1, p + 1))
  const goLast = () => setCurrentPage(Math.max(0, totalPages - 1))

  return (
    <div className="daily-activity-wrapper">
      <CCard>
        <CCardBody>

          <div className="ag-grid-container ag-theme-quartz no-grid-border"
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

          {/* Pagination controls */}
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
import { set } from 'lodash';

const DailyMessageDialog = ({ open, onClose }) => {
    // State for the selected date (defaults to today)
    const [selectedDate, setSelectedDate] = useState(() => {
        // Initializes the selected date to today's date in YYYY-MM-DD format
        const today = new Date();
        return today.toISOString().slice(0, 10);
    });
    // State for the message text
    const [messageText, setMessageText] = useState('');
    // State for the original message text
    const [originalMessageText, setOriginalMessageText] = useState('');
    // Snackbar state: 'none', 'add', 'update', 'delete', 'info'
    const [snackbarType, setSnackbarType] = useState('none');


    /**
     * Handles closing the dialog.
     * Prevents closing on backdrop click or escape key.
     * Calls the onClose prop otherwise.
     * @param {object} event - The event object.
     * @param {string} reason - The reason for closing.
     */
    const handleClose = (event, reason) => {
        if (reason === 'backdropClick' || reason === 'escapeKeyDown') {
            return;
        }
        setSnackbarType('none');
        setSelectedDate(new Date().toISOString().slice(0, 10));
        onClose();
    };

    /**
     * useEffect hook to fetch daily message details
     * whenever the dialog is opened or the selected date changes.
     */
    useEffect(() => {
        if (open && selectedDate) {
            getDailyMessageDetails(selectedDate);
        }
    }, [selectedDate, open]);

    /**
     * Fetches the daily message details for the selected date.
     * Updates the messageText and originalMessageText state.
     */
    const getDailyMessageDetails = async () => {
        try {
            const date = selectedDate + 'T00:00:00';
            const response = await dailyMessageService.fetchDailyMessageDetails(date);
            setMessageText(response[0].messageText);
            setOriginalMessageText(response[0].messageText);
        } catch (error) {
        }
    };

    /**
     * Handles saving the daily message.
     * Adds a new message if none exists, otherwise updates the existing message.
     * Updates the originalMessageText state after saving.
     */
    const onSave = async () => {
        try {
            const date = selectedDate + 'T00:00:00';
            const reqData = {
                messageText: messageText,
                messageDate: date
            }
            if (!originalMessageText) {
                await dailyMessageService.addDailyMessage(reqData);
                setSnackbarType('add');
            } else {
                await dailyMessageService.updateDailyMessage(reqData);
                setSnackbarType('update');
            }
            setOriginalMessageText(messageText);
        } catch (error) {
        }
    };

    /**
  * Handler for closing any open snackbar.
  * Resets all snackbar open states to false.
  */
    const handleSnackbarCancel = () => {
        setSnackbarType('none');
    };

    return (
        <>
            <Dialog open={open} onClose={handleClose} PaperProps={{ className: 'daily-message-dialog' }}>
                <DialogTitle><div className="daily-message-icon"><DailyMessage /></div>Daily Message</DialogTitle>
                <IconButton
                    aria-label="close"
                    onClick={handleClose}
                >
                    <CloseIcon />
                </IconButton>
                <DialogContent dividers>
                    <div className="date-layout">
                        <span className='date-text'>Date</span>
                        <CFormInput
                            type="date"
                            className='date-input'
                            value={selectedDate}
                            onChange={e => {setSelectedDate(e.target.value); setMessageText(''); setOriginalMessageText('');}}
                        />
                    </div>
                    <div>
                        <CFormTextarea
                            className='message-input'
                            value={messageText}
                            onChange={e => setMessageText(e.target.value)}
                            maxLength={250}
                        />
                        {messageText.length > 0 ? (<>
                            <span className="count-text"> Character count - </span> <span className='count-text count'> {messageText.length} / 250</span></>) : null}
                    </div>

                </DialogContent>
                <DialogActions>
                    <div className='daily-message-button-container'>
                        <Button variant="outlined" size="small" onClick={handleClose}>Cancel</Button>
                        <Button variant="contained" size="small" disabled={!messageText || messageText === originalMessageText} onClick={onSave}>Save</Button>
                    </div>
                </DialogActions>
            </Dialog>
            <CustomSnackbar
                type={snackbarType}
                open={snackbarType !== 'none'}
                handleOk={handleSnackbarCancel}
                onClose={handleSnackbarCancel}
                title={
                    (snackbarType === 'update' || snackbarType === 'add') ? 'Daily Message' : ''
                }
                body={
                    snackbarType === 'update' ? 'You have successfully Updated a Message Text' :
                        snackbarType === 'add' ? 'You have successfully Added a Message Text' : ''
                }
            />
        </>
    );
};

export default DailyMessageDialog;





