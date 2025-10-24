import { useEffect, useState } from 'react';
import AddIcon from '@mui/icons-material/Add';
import { Box } from '@mui/material';

import {
  CCard,
  CCardBody,
  CCol,
  CRow,
} from '@coreui/react';
import {
  TextField,
  FormControl,
  Select,
  MenuItem,
  Table,
  TableBody,
  TableCell,
  TableContainer,
  TableHead,
  TableRow,
  IconButton,
  Paper
} from '@mui/material';
import DeleteIcon from '@mui/icons-material/Delete';

import '../../../../scss/sys-prin-configuration/client-atm-pin-prefixes.scss';

import {
  unableToDeliver,
  forwardingAddress,
  nonUS,
  invalidState,
  isPOBox,
} from '../utils/FieldValueMapping';

const EditReMailOptions = ({ selectedData, setSelectedData, isEditable }) => {
  const getvalue = (field, fallback = '') => selectedData?.[field] ?? fallback;
  const compactCellSx = { py: 0.1, px: 1 }; // tighten vertical (py) & horizontal (px) padding

  const normaliseAreaArray = (arr) =>
    arr.map((area) =>
      typeof area === 'string' ? { area, sysPrin: selectedData.sysPrin ?? '' } : area
    );

  const getAreaNames = () =>
    getvalue('invalidDelivAreas', []).map((a) => (typeof a === 'string' ? a : a.area));

  const [selectedInvalidAreas, setSelectedInvalidAreas] = useState([]);

  useEffect(() => {
    setSelectedInvalidAreas(getAreaNames());
  }, [selectedData?.invalidDelivAreas]);

  // Debug helper (optional)
  useEffect(() => {
    if (!selectedData) return;
    try {
      // console.log('selectedData (JSON):\n' + JSON.stringify(selectedData, null, 2));
    } catch {}
  }, [selectedData]);

  const updateField = (field) => (value) =>
    setSelectedData((prev) => ({ ...prev, [field]: value }));

  // (Kept for reference; not used once we switched to table)
  const handleInvalidAreasChange = (e) => {
    const areas = Array.from(e.target.selectedOptions, (o) => o.value);
    setSelectedInvalidAreas(areas);
    updateField('invalidDelivAreas')(normaliseAreaArray(areas));
  };

  // Add a single area
  const handleAddArea = () => {
    if (!isEditable) return;
    const input = window.prompt('Enter area name to add:');
    if (input == null) return; // user cancelled
    const name = input.trim();
    if (!name) return;
    // prevent duplicates (case-insensitive)
    const exists = selectedInvalidAreas.some((n) => n.toLowerCase() === name.toLowerCase());
    if (exists) {
      alert('Area already exists.');
      return;
    }
    const newNames = [...selectedInvalidAreas, name];
    setSelectedInvalidAreas(newNames);
    updateField('invalidDelivAreas')(normaliseAreaArray(newNames));
  };

  // Delete a single area
  const handleDeleteArea = (areaName) => {
    const newNames = selectedInvalidAreas.filter((n) => n !== areaName);
    setSelectedInvalidAreas(newNames);
    updateField('invalidDelivAreas')(normaliseAreaArray(newNames));
  };

  const font78 = { fontSize: '0.73rem' };

  const leftLabel = {
    fontSize: '0.75rem',
    fontWeight: 500,
    minWidth: '160px', // tweak as needed
    marginLeft: '2px',
  };

  // NEW: only show scroll area when there are rows
  const hasAreas = selectedInvalidAreas.length > 0;

  return (
    <CRow className="d-flex justify-content-between align-items-stretch">
      {/* ----------- LEFT column ----------- */}
      <CCol xs={6} className="d-flex justify-content-start">
        <CCard className="mb-0 w-100 d-flex">
          <CCardBody className="d-flex flex-column" style={{ gap: '25px' }}>
            {/* Days to Hold — inline label on the left */}
            <div style={{ display: 'flex', alignItems: 'center', gap: '12px' }}>
              <div style={leftLabel}>Days to Hold</div>
              <TextField
                variant="outlined"
                fullWidth
                size="small"
                value={getvalue('holdDays')}
                onChange={(e) => updateField('holdDays')(e.target.value)}
                InputProps={{ sx: font78 }}
                disabled={!isEditable}
              />
            </div>

            {/* Days to Hold Temp Aways — inline */}
            <div style={{ display: 'flex', alignItems: 'center', gap: '12px' }}>
              <div style={leftLabel}>Days to Hold Temp Aways</div>
              <TextField
                variant="outlined"
                fullWidth
                size="small"
                value={getvalue('tempAway')}
                onChange={(e) => updateField('tempAway')(e.target.value)}
                InputProps={{ sx: font78 }}
                disabled={!isEditable}
              />
            </div>

            {/* Re-Mail Attempts — inline */}
            <div style={{ display: 'flex', alignItems: 'center', gap: '12px' }}>
              <div style={leftLabel}>Re-Mail Attempts</div>
              <TextField
                variant="outlined"
                fullWidth
                size="small"
                value={getvalue('tempAwayAtts')}
                onChange={(e) => updateField('tempAwayAtts')(e.target.value)}
                InputProps={{ sx: font78 }}
                disabled={!isEditable}
              />
            </div>

            {/* Unable to Deliver — inline */}
            <div style={{ display: 'flex', alignItems: 'center', gap: '12px' }}>
              <div style={{ ...leftLabel, whiteSpace: 'nowrap' }}>Unable to Deliver</div>
              <FormControl fullWidth size="small" disabled={!isEditable} sx={{ flex: 1 }}>
                <Select
                  labelId="undeliverable-label"
                  id="undeliverable"
                  value={getvalue('undeliverable')}
                  onChange={(e) => updateField('undeliverable')(e.target.value)}
                  sx={font78}
                >
                  {unableToDeliver.map((opt) => (
                    <MenuItem key={opt.code} value={opt.code} sx={font78}>
                      {opt.label}
                    </MenuItem>
                  ))}
                </Select>
              </FormControl>
            </div>

            {/* Forwarding Address — inline */}
            <div style={{ display: 'flex', alignItems: 'center', gap: '12px' }}>
              <div style={{ ...leftLabel, whiteSpace: 'nowrap' }}>Forwarding Address</div>
              <FormControl fullWidth size="small" disabled={!isEditable} sx={{ flex: 1 }}>
                <Select
                  labelId="forwarding-label"
                  id="forwarding"
                  value={getvalue('forwardingAddress')}
                  onChange={(e) => updateField('forwardingAddress')(e.target.value)}
                  sx={font78}
                >
                  {forwardingAddress.map((opt) => (
                    <MenuItem key={opt.code} value={opt.code} sx={font78}>
                      {opt.label}
                    </MenuItem>
                  ))}
                </Select>
              </FormControl>
            </div>
          </CCardBody>
        </CCard>
      </CCol>

      {/* ----------- RIGHT column ----------- */}
      <CCol xs={6} className="d-flex justify-content-end">
        <CCard className="mb-0 w-100" style={{ height: 'auto' }}>
          <CCardBody className="d-flex flex-column gap-3">

            {/* Do Not Deliver grid table — scrollable container only when there are rows */}
            <TableContainer
              component={Paper}
              variant="outlined"
              sx={{
                /* keep everything you already have */
                height: 120,
                overflowY: 'auto',
                scrollbarWidth: 'thin',
                scrollbarColor: '#cfd8dc #f5f7fa',
                '&::-webkit-scrollbar': { width: 8, height: 8 },
                '&::-webkit-scrollbar-track': { backgroundColor: '#f5f7fa', borderRadius: 8 },
                '&::-webkit-scrollbar-thumb': { backgroundColor: '#cfd8dc', borderRadius: 8, border: '2px solid #f5f7fa' },
                '&::-webkit-scrollbar-thumb:hover': { backgroundColor: '#bfcbd3' },

                borderTop: 'none',
                borderLeft: 'none',
                borderRight: 'none',

                /* needed so the overlay positions inside this box */
                position: 'relative',
              }}
            >
              <Table size="small" stickyHeader aria-label="Do Not Deliver table">
                <TableHead
                  sx={{
                    '& .MuiTableCell-root': {
                      borderTop: 'none',
                      borderLeft: 'none',
                      borderRight: 'none',
                      borderBottom: '1px solid',
                      borderColor: 'divider',
                    },
                  }}
                >
                  <TableRow sx={{ '& th': compactCellSx }}>
                    <TableCell sx={{ ...compactCellSx, ...font78 }}>
                      <span style={{ color: 'red' }}>Do Not Deliver to ...</span>
                    </TableCell>
                    <TableCell sx={{ ...compactCellSx }} align="right">
                      <IconButton
                        aria-label="Add area"
                        onClick={handleAddArea}
                        disabled={!isEditable}
                        size="small"
                        sx={{
                          width: 22,
                          height: 22,
                          p: 0,
                          border: '1px solid #1976d2',
                          bgcolor: '#fff',
                          color: '#1976d2',
                          borderRadius: '6px',
                          '&:hover': { bgcolor: '#e3f2fd' },
                          '&.Mui-disabled': {
                            borderColor: 'divider',
                            color: 'action.disabled',
                            bgcolor: 'transparent',
                          },
                        }}
                      >
                        <AddIcon sx={{ fontSize: 14 }} />
                      </IconButton>
                    </TableCell>
                  </TableRow>
                </TableHead>

                <TableBody>
                  {hasAreas ? (
                    selectedInvalidAreas.map((name, idx) => (
                      <TableRow key={`${name}-${idx}`} sx={{ '& td': compactCellSx }}>
                        <TableCell sx={{ ...compactCellSx, ...font78 }}>{name}</TableCell>
                        <TableCell sx={{ ...compactCellSx }} align="right">
                          <IconButton
                            size="small"
                            aria-label={`Delete ${name}`}
                            onClick={() => handleDeleteArea(name)}
                            disabled={!isEditable}
                          >
                            <DeleteIcon fontSize="small" />
                          </IconButton>
                        </TableCell>
                      </TableRow>
                    ))
                  ) : null}
                </TableBody>
              </Table>

              {/* Centered message (not inside a cell), below the header */}
              {!hasAreas && (
                <Box
                  sx={{
                    position: 'absolute',
                    left: 0,
                    right: 0,
                    top: 36,      // adjust if your header row is taller/shorter
                    bottom: 0,
                    display: 'flex',
                    alignItems: 'center',
                    justifyContent: 'center',
                    pointerEvents: 'none',
                    zIndex: 1,
                  }}
                >
                  <em style={{ fontSize: '0.73rem', color: 'rgba(0,0,0,0.6)' }}>
                    No areas selected.
                  </em>
                </Box>
              )}
            </TableContainer>

            {/* Non-US — inline */}
            <div style={{ display: 'flex', alignItems: 'center', gap: '12px' }}>
              <div style={{ ...leftLabel, whiteSpace: 'nowrap' }}>Non-US</div>
              <FormControl fullWidth size="small" disabled={!isEditable} sx={{ flex: 1 }}>
                <Select
                  labelId="nonus-label"
                  id="nonus"
                  value={getvalue('nonUS')}
                  onChange={(e) => updateField('nonUS')(e.target.value)}
                  sx={font78}
                >
                  {nonUS.map((opt) => (
                    <MenuItem key={opt.code} value={opt.code} sx={font78}>
                      {opt.label}
                    </MenuItem>
                  ))}
                </Select>
              </FormControl>
            </div>

            {/* Address is P.O. Box — inline */}
            <div style={{ display: 'flex', alignItems: 'center', gap: '12px' }}>
              <div style={{ ...leftLabel, whiteSpace: 'nowrap' }}>Address is P.O. Box</div>
              <FormControl fullWidth size="small" disabled={!isEditable} sx={{ flex: 1 }}>
                <Select
                  labelId="pobox-label"
                  id="pobox"
                  value={getvalue('poBox')}
                  onChange={(e) => updateField('poBox')(e.target.value)}
                  sx={font78}
                >
                  {isPOBox.map((opt) => (
                    <MenuItem key={opt.code} value={opt.code} sx={font78}>
                      {opt.label}
                    </MenuItem>
                  ))}
                </Select>
              </FormControl>
            </div>

            {/* Invalid State — inline */}
            <div style={{ display: 'flex', alignItems: 'center', gap: '12px' }}>
              <div style={{ ...leftLabel, whiteSpace: 'nowrap' }}>Invalid State</div>
              <FormControl fullWidth size="small" disabled={!isEditable} sx={{ flex: 1 }}>
                <Select
                  labelId="badstate-label"
                  id="badstate"
                  value={getvalue('badState')}
                  onChange={(e) => updateField('badState')(e.target.value)}
                  sx={font78}
                >
                  {invalidState.map((opt) => (
                    <MenuItem key={opt.code} value={opt.code} sx={font78}>
                      {opt.label}
                    </MenuItem>
                  ))}
                </Select>
              </FormControl>
            </div>
          </CCardBody>
        </CCard>
      </CCol>
    </CRow>
  );
};

export default EditReMailOptions;
