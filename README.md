// ClientReportWindow.jsx
import React, { useMemo, useState, useEffect } from 'react';
import {
  Dialog,
  DialogTitle,
  DialogContent,
  DialogActions,
  Button,
  Box,
  TextField,
  FormControl,
  Select,
  MenuItem,
  Typography,
} from '@mui/material';

// NEW: use your autocomplete input
import ClientReportAutoCompleteInputBox from '../../../../components/ClientReportAutoCompleteInputBox';

/** Centralized option maps (code -> label) */
const RECEIVE_OPTIONS = [
  { value: '0', label: 'None' },
  { value: '1', label: 'Yes' },
  { value: '2', label: 'No'  },
];

const DESTINATION_OPTIONS = [
  { value: '0', label: 'None' },
  { value: '1', label: 'File' },
  { value: '2', label: 'Print' },
];

const FILETYPE_OPTIONS = [
  { value: '0', label: 'None'  },
  { value: '1', label: 'Text'  },
  { value: '2', label: 'Excel' },
];

const EMAIL_OPTIONS = [
  { value: '0', label: 'None'  },
  { value: '1', label: 'Email' },
  { value: '2', label: 'Web'   },
];

const labelFor = (options, v) => (options.find(o => o.value === String(v))?.label ?? 'None');

/**
 * Props:
 * - open: boolean
 * - mode: 'detail' | 'edit' | 'new' | 'delete'
 * - row: { reportName, receive, destination, fileText, email, password } | undefined
 * - onClose: () => void
 * - onSave: (updatedRow) => void
 * - onDelete?: (rowToDelete) => void
 */
const ClientReportWindow = ({
  open,
  mode = 'detail',
  row,
  onClose,
  onSave,
  onDelete,
}) => {
  // default empty row (for "new" mode or fallback)
  const EMPTY_ROW = {
    reportName: '',
    receive: '0',
    destination: '0',
    fileText: '0',
    email: '0',
    password: '',
  };

  // normalize incoming row to expected string codes
  const normalize = (r) => ({
    reportName: r?.reportName ?? '',
    receive: r?.receive != null ? String(r.receive) : '0',
    destination: r?.destination != null ? String(r.destination) : '0',
    fileText: r?.fileText != null ? String(r.fileText) : '0',
    email: r?.email != null ? String(r.email) : '0',
    password: r?.password ?? '',
  });

  // choose the effective source for the form by mode
  const effectiveSource = mode === 'new' ? EMPTY_ROW : (row ?? EMPTY_ROW);

  const [form, setForm] = useState(normalize(effectiveSource));
  useEffect(() => {
    setForm(normalize(mode === 'new' ? EMPTY_ROW : (row ?? EMPTY_ROW)));
  }, [row, mode]);

  // NEW: local state to drive ClientReportAutoCompleteInputBox when in "new" mode
  const [reportNameInput, setReportNameInput] = useState(form.reportName || '');
  const [isWildcardMode, setIsWildcardMode] = useState(false);

  // keep form.reportName in sync with the ClientReportAutoCompleteInputBox value
  useEffect(() => {
    if (mode === 'new') {
      setForm((prev) => ({ ...prev, reportName: reportNameInput || '' }));
    }
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, [reportNameInput, mode]);

  const isReadOnly = mode === 'detail' || mode === 'delete';
  const isEditable = mode === 'edit' || mode === 'new';

  const cellSelectSx = {
    fontSize: '0.85rem',
    backgroundColor: 'white',
    '& .MuiSelect-select': { padding: '6px 10px', fontSize: '0.85rem' },
  };

  const labelSx = { fontSize: '0.8rem', color: '#666', mb: 0.5 };

  const handleChange = (key) => (e) =>
    setForm((prev) => ({ ...prev, [key]: String(e.target.value) }));

  // Avoid rendering until form is ready
  const ready = useMemo(() => !!form, [form]);

  // label cell (detail/delete modes)
  const renderLabel = (options, value) => (
    <Typography variant="body2" sx={{ fontSize: '0.9rem' }}>
      {labelFor(options, value)}
    </Typography>
  );

  // select cell (edit/new modes)
  const renderSelect = (options, value, key) => (
    <FormControl fullWidth size="small">
      <Select value={value} onChange={handleChange(key)} sx={cellSelectSx} disabled={!isEditable}>
        {options.map(opt => (
          <MenuItem key={opt.value} value={opt.value} sx={{ fontSize: '0.85rem' }}>
            {opt.label}
          </MenuItem>
        ))}
      </Select>
    </FormControl>
  );

  const titleText = (() => {
    switch (mode) {
      case 'edit': return 'Edit Client Report';
      case 'new': return 'New Client Report';
      case 'delete': return 'Delete Client Report';
      default: return 'Report Details';
    }
  })();

  const handlePrimaryAction = () => {
    if (mode === 'delete') {
      onDelete?.(form);
      return;
    }
    onSave?.(form);
  };

  return (
    <Dialog open={open} onClose={onClose} maxWidth="sm" fullWidth>
      {/* Compact blue header */}
      <DialogTitle
        sx={{
          bgcolor: '#1976d2',
          color: '#fff',
          py: 0.5,
          px: 1.5,
          minHeight: 'unset',
          display: 'flex',
          alignItems: 'center',
        }}
      >
        <Typography
          component="span"
          sx={{ fontSize: '0.9rem', lineHeight: 2, fontWeight: 400, color: 'inherit', mb: 0 }}
        >
          {titleText}
        </Typography>
      </DialogTitle>

      <DialogContent dividers>
        {!ready ? (
          <Typography variant="body2">Loading…</Typography>
        ) : (
          <Box display="grid" gridTemplateColumns="1fr 1fr" gap={2} mt={0.5}>
            {/* Report Name */}
            <Box gridColumn="1 / span 2">
              <Typography sx={labelSx}>Report Name</Typography>

              {mode === 'new' ? (
                // Replaces the Select with your ClientReportAutoCompleteInputBox
                <ClientReportAutoCompleteInputBox
                  inputValue={reportNameInput}
                  setInputValue={setReportNameInput}
                  // The next props are optional safety no-ops so your component
                  // renders without requiring client searches in this context.
                  onClientsFetched={() => {}}
                  isWildcardMode={isWildcardMode}
                  setIsWildcardMode={setIsWildcardMode}
                  sx={{
                    '& .MuiOutlinedInput-root': {
                      '& fieldset': { border: '1px solid rgba(0,0,0,0.23)' },
                    },
                  }}
                />
              ) : isEditable ? (
                <TextField
                  value={form.reportName}
                  onChange={handleChange('reportName')}
                  size="small"
                  fullWidth
                />
              ) : (
                <Typography variant="body2" sx={{ fontSize: '0.9rem' }}>
                  {form.reportName || '(empty)'}
                </Typography>
              )}
            </Box>

            {/* Receive */}
            <Box>
              <Typography sx={labelSx}>Receive</Typography>
              {isEditable
                ? renderSelect(RECEIVE_OPTIONS, form.receive, 'receive')
                : renderLabel(RECEIVE_OPTIONS, form.receive)}
            </Box>

            {/* Destination */}
            <Box>
              <Typography sx={labelSx}>Destination</Typography>
              {isEditable
                ? renderSelect(DESTINATION_OPTIONS, form.destination, 'destination')
                : renderLabel(DESTINATION_OPTIONS, form.destination)}
            </Box>

            {/* File Type */}
            <Box>
              <Typography sx={labelSx}>File Type</Typography>
              {isEditable
                ? renderSelect(FILETYPE_OPTIONS, form.fileText, 'fileText')
                : renderLabel(FILETYPE_OPTIONS, form.fileText)}
            </Box>

            {/* Email */}
            <Box>
              <Typography sx={labelSx}>Email</Typography>
              {isEditable
                ? renderSelect(EMAIL_OPTIONS, form.email, 'email')
                : renderLabel(EMAIL_OPTIONS, form.email)}
            </Box>

            {/* Password */}
            <Box gridColumn="1 / span 2">
              <Typography sx={labelSx}>Password</Typography>
              {isEditable ? (
                <TextField
                  type="password"
                  value={form.password}
                  onChange={handleChange('password')}
                  size="small"
                  fullWidth
                />
              ) : (
                <Typography variant="body2" sx={{ fontSize: '0.9rem' }}>
                  {form.password ? '••••••••' : '(empty)'}
                </Typography>
              )}
            </Box>

            {mode === 'delete' && (
              <Box gridColumn="1 / span 2" sx={{ mt: 1 }}>
                <Typography variant="body2" color="error" sx={{ fontSize: '0.9rem' }}>
                  This action cannot be undone. Do you want to permanently delete this report?
                </Typography>
              </Box>
            )}
          </Box>
        )}
      </DialogContent>

      <DialogActions>
        <Button onClick={onClose} variant="outlined" size="small">
          {mode === 'delete' ? 'Cancel' : 'Close'}
        </Button>

        {mode === 'edit' && (
          <Button onClick={handlePrimaryAction} variant="contained" size="small">
            Save
          </Button>
        )}

        {mode === 'new' && (
          <Button
            onClick={handlePrimaryAction}
            variant="contained"
            size="small"
            disabled={!form.reportName} // still prevent creating without a selection/text
          >
            Create
          </Button>
        )}

        {mode === 'delete' && (
          <Button onClick={handlePrimaryAction} variant="contained" color="error" size="small">
            Delete
          </Button>
        )}
      </DialogActions>
    </Dialog>
  );
};

export default ClientReportWindow;




curl -X 'POST' \
  'http://localhost:8089/client-sysprin-writer/api/client/reportOptions/update' \
  -H 'accept: */*' \
  -H 'Content-Type: application/json' \
  -d '{
  "clientId": "0029",
  "reportId": 121,
  "receiveFlag": false,
  "outputTypeCd": 0,
  "fileTypeCd": 0,
  "emailFlag": 0,
  "emailBodyTx": "email body updated",
  "reportPasswordTx": "updates report for 0029"
}'



curl -X 'POST' \
  'http://localhost:8089/client-sysprin-writer/api/client/reportOptions/Initiate' \
  -H 'accept: */*' \
  -H 'Content-Type: application/json' \
  -d '{
    "clientId": "0029",
    "reportId": 115,
    "receiveFlag": false,
    "outputTypeCd": 0,
    "fileTypeCd": 0,
    "emailFlag": 0,
    "emailBodyTx": null,
    "reportPasswordTx": "new report for 0029"
}'







// EditClientReport.jsx
import React, { useEffect, useMemo, useState } from 'react';
import { Typography, Button, IconButton, Tooltip } from '@mui/material';
import InfoOutlinedIcon from '@mui/icons-material/InfoOutlined';
import EditOutlinedIcon from '@mui/icons-material/EditOutlined';
import DeleteOutlineIcon from '@mui/icons-material/DeleteOutline';
import { CCard, CCardBody } from '@coreui/react';

import ClientReportWindow from './utils/ClientReportWindow';

const PAGE_SIZE = 10;

const EditClientReport = ({ selectedGroupRow, isEditable }) => {
  const [tableData, setTableData] = useState([]);
  const [page, setPage] = useState(0);

  // modal for detail/edit/new/delete
  const [modalOpen, setModalOpen] = useState(false);
  const [modalMode, setModalMode] = useState('detail'); // 'detail' | 'edit' | 'new' | 'delete'
  const [modalRowIdx, setModalRowIdx] = useState(null); // null => creating new
  const [draftRow, setDraftRow] = useState(null);       // holds the "New" row

  // pagination
  const pageCount = useMemo(() => Math.ceil((tableData?.length || 0) / PAGE_SIZE), [tableData]);
  const pageData = useMemo(
    () => tableData.slice(page * PAGE_SIZE, (page + 1) * PAGE_SIZE),
    [tableData, page]
  );

  // load/normalize incoming data
  useEffect(() => {
    const options = Array.isArray(selectedGroupRow?.reportOptions)
      ? selectedGroupRow.reportOptions
      : [];

    const clientData = options.map((option) => ({
      reportName: option?.reportDetails?.queryName || '',
      receive: option?.receiveFlag ? '1' : '2', // 1=Yes, 2=No
      destination: option?.outputTypeCd !== undefined ? String(option.outputTypeCd) : '0', // 0=None,1=File,2=Print
      fileText: option?.fileTypeCd !== undefined ? String(option.fileTypeCd) : '0',       // 0=None,1=Text,2=Excel
      email: option?.emailFlag === 1 ? '1' : option?.emailFlag === 2 ? '2' : '0',        // 0=None,1=Email,2=Web
      password: option?.reportPasswordTx || '',
    }));

    setTableData(clientData);
    const lastPage = Math.max(Math.ceil(clientData.length / PAGE_SIZE) - 1, 0);
    setPage((p) => Math.min(p, lastPage));
  }, [selectedGroupRow]);

  // styles
  const rowCellStyle = {
    backgroundColor: 'white',
    fontSize: '0.72rem',
    lineHeight: 1.1,
    padding: '2px 6px',
    borderBottom: '1px dotted #ddd',
    display: 'flex',
    alignItems: 'center',
    whiteSpace: 'nowrap',
    overflow: 'hidden',
    textOverflow: 'ellipsis',
  };
  const headerCellStyle = {
    backgroundColor: '#f0f0f0',
    fontWeight: 'bold',
    fontSize: '0.75rem',
    padding: '2px 6px',
    borderBottom: '1px dotted #ccc',
  };
  const labelCell = (text, align = 'center') => (
    <div style={{ ...rowCellStyle, justifyContent: align }}>
      <Typography noWrap sx={{ fontSize: '0.72rem', lineHeight: 1.1 }}>
        {text}
      </Typography>
    </div>
  );

  // label mappers
  const mapReceive = (v) => (v === '1' ? 'Yes' : v === '2' ? 'No' : 'None');
  const mapDestination = (v) => (v === '1' ? 'File' : v === '2' ? 'Print' : 'None');

  // actions -> detail/edit/delete
  const openDetail = (rowIdx) => {
    setModalMode('detail');
    setModalRowIdx(rowIdx);
    setDraftRow(null);
    setModalOpen(true);
  };
  const openEdit = (rowIdx) => {
    setModalMode('edit');
    setModalRowIdx(rowIdx);
    setDraftRow(null);
    setModalOpen(true);
  };
  const openDelete = (rowIdx) => {
    setModalMode('delete');
    setModalRowIdx(rowIdx);
    setDraftRow(null);
    setModalOpen(true);
  };

  // actual deletion logic (also used by delete-confirm in modal)
  const handleRemove = (rowIdx) => {
    setTableData((prev) => {
      const next = prev.filter((_, i) => i !== rowIdx);
      const lastPage = Math.max(Math.ceil(next.length / PAGE_SIZE) - 1, 0);
      setPage((p) => Math.min(p, lastPage));
      return next;
    });
  };

  // "New" button -> open modal in new mode with blank row
  const handleNewClick = () => {
    if (!isEditable) return;
    setModalMode('new');
    setModalRowIdx(null); // null signals "create"
    setDraftRow({
      reportName: '',
      receive: '0',
      destination: '0',
      fileText: '0',
      email: '0',
      password: '',
    });
    setModalOpen(true);
  };

  // save/create from modal
  const handleSaveFromModal = (updatedRow) => {
    if (modalRowIdx == null) {
      // creating a new row
      const normalized = {
        reportName: updatedRow?.reportName ?? '',
        receive: updatedRow?.receive != null ? String(updatedRow.receive) : '0',
        destination: updatedRow?.destination != null ? String(updatedRow.destination) : '0',
        fileText: updatedRow?.fileText != null ? String(updatedRow.fileText) : '0',
        email: updatedRow?.email != null ? String(updatedRow.email) : '0',
        password: updatedRow?.password ?? '',
      };
      setTableData((prev) => {
        const next = [...prev, normalized];
        const lastPage = Math.max(Math.ceil(next.length / PAGE_SIZE) - 1, 0);
        setPage(lastPage);
        return next;
      });
    } else {
      // updating existing row
      setTableData((prev) => {
        const next = [...prev];
        next[modalRowIdx] = { ...next[modalRowIdx], ...updatedRow };
        return next;
      });
    }
    setModalOpen(false);
    setDraftRow(null);
  };

  // delete confirm from modal
  const handleDeleteFromModal = () => {
    if (modalRowIdx == null) return;
    handleRemove(modalRowIdx);
    setModalOpen(false);
    setDraftRow(null);
  };

  const currentRow = modalRowIdx == null ? draftRow : tableData[modalRowIdx] || null;

  return (
    <div style={{ padding: '10px' }}>
      <CCard>
        <CCardBody>
          {/* pagination (top) */}
          <div
            style={{
              marginBottom: '6px',
              display: 'flex',
              justifyContent: 'space-between',
              alignItems: 'center',
            }}
          >
            <Button
              variant="text"
              size="small"
              sx={{ fontSize: '0.68rem', padding: '2px 6px', minWidth: 'unset', textTransform: 'none' }}
              onClick={() => setPage((p) => Math.max(p - 1, 0))}
              disabled={page === 0 || pageCount === 0}
            >
              ◀ Previous
            </Button>
            <Typography fontSize="0.72rem">
              Page {tableData.length > 0 ? page + 1 : 0} of {tableData.length > 0 ? pageCount : 0}
            </Typography>
            <Button
              variant="text"
              size="small"
              sx={{ fontSize: '0.68rem', padding: '2px 6px', minWidth: 'unset', textTransform: 'none' }}
              onClick={() => setPage((p) => Math.min(p + 1, pageCount - 1))}
              disabled={page >= pageCount - 1 || pageCount === 0}
            >
              Next ▶
            </Button>
          </div>

          {/* table */}
          <div style={{ height: '320px', marginBottom: '4px', marginTop: '2px' }}>
            <div
              style={{
                display: 'grid',
                gridTemplateColumns: '4fr 1fr 1fr 3fr',
                columnGap: '4px',
              }}
            >
              {/* headers */}
              {['Report Name', 'Receive', 'Destination', 'Action'].map((header) => (
                <div
                  key={header}
                  style={{
                    ...headerCellStyle,
                    textAlign: header === 'Action' ? 'center' : 'left',
                  }}
                >
                  {header}
                </div>
              ))}

              {/* rows */}
              {pageData.map((item, index) => {
                const rowIdx = page * PAGE_SIZE + index;
                return (
                  <React.Fragment key={`${item.reportName}-${rowIdx}`}>
                    <div style={rowCellStyle}>{item.reportName}</div>
                    {labelCell(mapReceive(item.receive))}
                    {labelCell(mapDestination(item.destination))}
                    <div style={{ ...rowCellStyle, gap: 4, justifyContent: 'center' }}>
                      <Tooltip title="Detail" arrow>
                        <IconButton
                          aria-label="detail"
                          size="small"
                          sx={{ p: 0.25, fontSize: '0.95rem' }}
                          onClick={() => openDetail(rowIdx)}
                        >
                          <InfoOutlinedIcon fontSize="inherit" />
                        </IconButton>
                      </Tooltip>
                      <Tooltip title="Edit" arrow>
                        <span>
                          <IconButton
                            aria-label="edit"
                            size="small"
                            sx={{ p: 0.25, fontSize: '0.95rem' }}
                            onClick={() => openEdit(rowIdx)}
                            disabled={!isEditable}
                          >
                            <EditOutlinedIcon fontSize="inherit" />
                          </IconButton>
                        </span>
                      </Tooltip>
                      <Tooltip title="Remove" arrow>
                        <span>
                          <IconButton
                            aria-label="remove"
                            size="small"
                            sx={{ p: 0.25, fontSize: '0.95rem' }}
                            onClick={() => openDelete(rowIdx)} // open delete mode window
                            disabled={!isEditable}
                          >
                            <DeleteOutlineIcon fontSize="inherit" />
                          </IconButton>
                        </span>
                      </Tooltip>
                    </div>
                  </React.Fragment>
                );
              })}
            </div>
          </div>

          {/* "New" button centered horizontally */}
          <div style={{ marginTop: '8px', display: 'flex', justifyContent: 'center' }}>
            <Button
              variant="contained"
              size="small"
              onClick={handleNewClick}
              disabled={!isEditable}
            >
              New
            </Button>
          </div>
        </CCardBody>
      </CCard>

      {/* detail/edit/new/delete window */}
      <ClientReportWindow
        open={modalOpen}
        mode={modalMode}
        row={currentRow}
        onClose={() => {
          setModalOpen(false);
          setDraftRow(null);
        }}
        onSave={handleSaveFromModal}          // used for 'edit' and 'new'
        onDelete={handleDeleteFromModal}      // used for 'delete'
      />
    </div>
  );
};

export default EditClientReport;







