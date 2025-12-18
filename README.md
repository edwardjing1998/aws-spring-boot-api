import React, { useEffect, useMemo, useRef, useState } from 'react';
import AddIcon from '@mui/icons-material/Add';
import DeleteIcon from '@mui/icons-material/Delete';
import { Box } from '@mui/material';

import {
  CCard, CCardBody, CCol, CRow,
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
  Paper,
  SelectChangeEvent
} from '@mui/material';

// import '../../../../../scss/sys-prin-configuration/client-atm-pin-prefixes.scss';

import {
  unableToDeliver,
  forwardingAddress,
  nonUS,
  invalidState,
  isPOBox,
} from '../../utils/ClientInfoFieldValueMapping';

import EditInvalidedAreaWindow from '../../utils/EditInvalidedAreaWindow';

// --- Interfaces ---

interface InvalidAreaItem {
  area: string;
  sysPrin: string;
}

interface SysPrinData {
  client?: string;
  sysPrin?: string;
  invalidDelivAreas?: (string | InvalidAreaItem)[];
  holdDays?: number | string;
  tempAway?: number | string;
  tempAwayAtts?: number | string;
  undeliverable?: string;
  forwardingAddress?: string;
  nonUS?: string;
  poBox?: string;
  badState?: string;
  custType?: string;
  returnStatus?: string;
  destroyStatus?: string;
  special?: string;
  pinMailer?: string;
  sysPrinActive?: boolean | string;
  rps?: boolean | string;
  addrFlag?: boolean | string;
  astatRch?: boolean | string;
  nm13?: boolean | string;
  notes?: string;
  reportMethod?: number | string;
  session?: string;
  forwardingAddressLine?: string;
  entityCode?: string;
  sysPrinContact?: string;
  sysPrinPhone?: string;
  statA?: string;
  statB?: string;
  statC?: string;
  statD?: string;
  statE?: string;
  statF?: string;
  statI?: string;
  statL?: string;
  statO?: string;
  statU?: string;
  statX?: string;
  statZ?: string;
  [key: string]: any;
}

interface EditReMailOptionsProps {
  selectedData: SysPrinData | null;
  setSelectedData: React.Dispatch<React.SetStateAction<any>>;
  isEditable: boolean;
  onChangeGeneral: (patch: any) => void;
}

const EditReMailOptions: React.FC<EditReMailOptionsProps> = ({ 
  selectedData, 
  setSelectedData, 
  isEditable, 
  onChangeGeneral 
}) => {
  // eslint-disable-next-line @typescript-eslint/no-unused-vars
  const [updating, setUpdating] = useState(false);

  // central helper (same pattern as General)
  const pushGeneralPatch = (patch: any) => {
    const withKeys = {
      client: selectedData?.client,
      sysPrin: selectedData?.sysPrin,
      ...patch,
    };
    if (typeof onChangeGeneral === 'function') onChangeGeneral(withKeys);
    else setSelectedData((prev: any) => ({ ...(prev ?? {}), ...withKeys }));
  };

  const getvalue = (field: keyof SysPrinData, fallback: any = '') => selectedData?.[field] ?? fallback;
  const compactCellSx = { py: 0.1, px: 1 };

  const normaliseAreaArray = (arr: (string | InvalidAreaItem)[]) =>
    arr.map((area) =>
      typeof area === 'string' ? { area, sysPrin: selectedData?.sysPrin ?? '' } : area
    );

  const getAreaNames = () =>
    (getvalue('invalidDelivAreas', []) as (string | InvalidAreaItem)[]).map((a) => (typeof a === 'string' ? a : a.area));

  const [selectedInvalidAreas, setSelectedInvalidAreas] = useState<string[]>([]);
  const [openAreaWindow, setOpenAreaWindow] = useState(false);
  const [dialogMode, setDialogMode] = useState<'create' | 'delete'>('create');
  const [dialogInitialArea, setDialogInitialArea] = useState('');

  useEffect(() => {
    setSelectedInvalidAreas(getAreaNames());
  }, [selectedData?.invalidDelivAreas]);

  const updateField = (field: string) => (value: any) => pushGeneralPatch({ [field]: value });

  // Add → open in create mode
  const handleAddArea = () => {
    if (!isEditable) return;
    setDialogMode('create');
    setDialogInitialArea('');
    setOpenAreaWindow(true);
  };

  // Delete → open in delete mode with the clicked area
  const handleOpenDelete = (areaName: string) => {
    if (!isEditable) return;
    setDialogMode('delete');
    setDialogInitialArea(areaName);
    setOpenAreaWindow(true);
  };

  // Local removal (not used directly because we use dialog)
  // eslint-disable-next-line @typescript-eslint/no-unused-vars
  const handleDeleteArea = (areaName: string) => {
    const newNames = selectedInvalidAreas.filter((n) => n !== areaName);
    setSelectedInvalidAreas(newNames);
    updateField('invalidDelivAreas')(normaliseAreaArray(newNames));
  };

  const font78 = { fontSize: '0.73rem' };
  const leftLabel = {
    fontSize: '0.75rem',
    fontWeight: 500,
    minWidth: '160px',
    marginLeft: '2px',
  };

  const hasAreas = selectedInvalidAreas.length > 0;

  // ---------- Build payload (merge page fields with everything else so nothing is lost) ----------
  const buildPayload = useMemo(() => {
    const sd = selectedData ?? {};
    const toBool = (v: any) => (v === true || v === 'Y'); // backend expects boolean for `active`
    const to10  = (v: any) => (v === true || v === '1') ? '1' : (v === '0' || v === false ? '0' : (v ?? '0'));
    const toYN  = (v: any) => (v === true || v === 'Y') ? 'Y' : (v === false || v === 'N' ? 'N' : (v ?? 'N'));

    return {
      // identity
      client: sd.client ?? '',
      sysPrin: sd.sysPrin ?? '',

      // values from THIS page (use the latest values in selectedData)
      holdDays: Number(sd.holdDays ?? 0),
      tempAway: Number(sd.tempAway ?? 0),
      tempAwayAtts: Number(sd.tempAwayAtts ?? 0),
      undeliverable: sd.undeliverable ?? '0',
      forwardingAddress: sd.forwardingAddress ?? '0',
      nonUS: sd.nonUS ?? '0',
      poBox: sd.poBox ?? '0',
      badState: sd.badState ?? '0',
      // NOTE: invalidDelivAreas is managed by its own create/delete APIs; not included in this PUT.

      // everything else — keep unchanged
      custType: sd.custType ?? '0',
      returnStatus: sd.returnStatus ?? '',
      destroyStatus: sd.destroyStatus ?? '0',
      special: sd.special ?? '0',
      pinMailer: sd.pinMailer ?? '0',
      sysPrinActive: toBool(sd.sysPrinActive),
      rps: toYN(sd.rps),
      addrFlag: toYN(sd.addrFlag),
      astatRch: to10(sd.astatRch),
      nm13: to10(sd.nm13),
      notes: sd.notes ?? '',
      reportMethod: Number(sd.reportMethod ?? 0),
      session: sd.session ?? '',
      forwardingAddressLine: sd.forwardingAddressLine ?? '', // if your API uses this; else remove
      entityCode: sd.entityCode ?? '0',
      sysPrinContact: sd.sysPrinContact ?? '',
      sysPrinPhone: sd.sysPrinPhone ?? '',

      // status letters (unchanged here)
      statA: sd.statA ?? '0',
      statB: sd.statB ?? '0',
      statC: sd.statC ?? '0',
      statD: sd.statD ?? '0',
      statE: sd.statE ?? '0',
      statF: sd.statF ?? '0',
      statI: sd.statI ?? '0',
      statL: sd.statL ?? '0',
      statO: sd.statO ?? '0',
      statU: sd.statU ?? '0',
      statX: sd.statX ?? '0',
      statZ: sd.statZ ?? '0',
    };
  }, [selectedData]);

  // eslint-disable-next-line @typescript-eslint/no-unused-vars
  const handleUpdate = async () => {
    const client = selectedData?.client;
    const sysPrinCode = selectedData?.sysPrin;
    if (!client || !sysPrinCode) {
      alert('Missing client or sysPrin.');
      return;
    }

    const url = `http://localhost:8089/client-sysprin-writer/api/sysprins/update/${encodeURIComponent(client)}/${encodeURIComponent(sysPrinCode)}`;

    setUpdating(true);
    try {
      const res = await fetch(url, {
        method: 'PUT',
        headers: { accept: '*/*', 'Content-Type': 'application/json' },
        body: JSON.stringify(buildPayload),
      });

      if (!res.ok) {
        let msg = `Update failed (${res.status})`;
        try {
          const ct = res.headers.get('Content-Type') || '';
          if (ct.includes('application/json')) {
            const j = await res.json();
            msg = j?.message || JSON.stringify(j);
          } else {
            msg = await res.text();
          }
        } catch {}
        throw new Error(msg);
      }

      // ✅ define `saved` safely (API may or may not return JSON)
      let saved = null;
      try {
        const ct = res.headers.get('Content-Type') || '';
        if (ct.includes('application/json')) {
          saved = await res.json();
        }
      } catch {}

      // Build the canonical patch for fields this page owns
      const patch = saved ?? {
        holdDays: buildPayload.holdDays,
        tempAway: buildPayload.tempAway,
        tempAwayAtts: buildPayload.tempAwayAtts,
        undeliverable: buildPayload.undeliverable,
        forwardingAddress: buildPayload.forwardingAddress,
        nonUS: buildPayload.nonUS,
        poBox: buildPayload.poBox,
        badState: buildPayload.badState,
        // invalidDelivAreas handled by its own create/delete APIs
      };

      // Bubble up so parent list stays in sync
      pushGeneralPatch(patch);

      alert('Re-mail options updated successfully.');
    } catch (e: any) {
      console.error(e);
      alert(e?.message || 'Failed to update.');
    } finally {
      setUpdating(false);
    }
  };

  return (
    <>
      <CRow className="d-flex justify-content-between align-items-stretch">
        {/* ----------- LEFT column ----------- */}
        <CCol xs={6} className="d-flex justify-content-start">
          <CCard className="mb-0 w-100 d-flex">
            <CCardBody className="d-flex flex-column" style={{ gap: '25px' }}>
              {/* Days to Hold */}
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

              {/* Days to Hold Temp Aways */}
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

              {/* Re-Mail Attempts */}
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

              {/* Unable to Deliver */}
              <div style={{ display: 'flex', alignItems: 'center', gap: '12px' }}>
                <div style={{ ...leftLabel, whiteSpace: 'nowrap' }}>Unable to Deliver</div>
                <FormControl fullWidth size="small" disabled={!isEditable} sx={{ flex: 1 }}>
                  <Select
                    labelId="undeliverable-label"
                    id="undeliverable"
                    value={getvalue('undeliverable')}
                    onChange={(e: SelectChangeEvent) => updateField('undeliverable')(e.target.value)}
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

              {/* Forwarding Address */}
              <div style={{ display: 'flex', alignItems: 'center', gap: '12px' }}>
                <div style={{ ...leftLabel, whiteSpace: 'nowrap' }}>Forwarding Address</div>
                <FormControl fullWidth size="small" disabled={!isEditable} sx={{ flex: 1 }}>
                  <Select
                    labelId="forwarding-label"
                    id="forwarding"
                    value={getvalue('forwardingAddress')}
                    onChange={(e: SelectChangeEvent) => updateField('forwardingAddress')(e.target.value)}
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
              <TableContainer
                component={Paper}
                variant="outlined"
                sx={{
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
                              onClick={() => handleOpenDelete(name)}  // open delete dialog
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

                {!hasAreas && (
                  <Box
                    sx={{
                      position: 'absolute',
                      left: 0,
                      right: 0,
                      top: 36,
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

              {/* Non-US */}
              <div style={{ display: 'flex', alignItems: 'center', gap: '12px' }}>
                <div style={{ ...leftLabel, whiteSpace: 'nowrap' }}>Non-US</div>
                <FormControl fullWidth size="small" disabled={!isEditable} sx={{ flex: 1 }}>
                  <Select
                    labelId="nonus-label"
                    id="nonus"
                    value={getvalue('nonUS')}
                    onChange={(e: SelectChangeEvent) => updateField('nonUS')(e.target.value)}
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

              {/* Address is P.O. Box */}
              <div style={{ display: 'flex', alignItems: 'center', gap: '12px' }}>
                <div style={{ ...leftLabel, whiteSpace: 'nowrap' }}>Address is P.O. Box</div>
                <FormControl fullWidth size="small" disabled={!isEditable} sx={{ flex: 1 }}>
                  <Select
                    labelId="pobox-label"
                    id="pobox"
                    value={getvalue('poBox')}
                    onChange={(e: SelectChangeEvent) => updateField('poBox')(e.target.value)}
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

              {/* Invalid State */}
              <div style={{ display: 'flex', alignItems: 'center', gap: '12px' }}>
                <div style={{ ...leftLabel, whiteSpace: 'nowrap' }}>Invalid State</div>
                <FormControl fullWidth size="small" disabled={!isEditable} sx={{ flex: 1 }}>
                  <Select
                    labelId="badstate-label"
                    id="badstate"
                    value={getvalue('badState')}
                    onChange={(e: SelectChangeEvent) => updateField('badState')(e.target.value)}
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

      {/* --- Add/Delete Invalid Area Dialog --- */}
      <EditInvalidedAreaWindow
        open={openAreaWindow}
        onClose={() => setOpenAreaWindow(false)}
        sysPrin={selectedData?.sysPrin || ''}
        mode={dialogMode}
        initialArea={dialogInitialArea}
        onCreated={(areaCode: string) => {
          const exists = selectedInvalidAreas.some(
            (n) => String(n).toLowerCase() === String(areaCode).toLowerCase()
          );
          if (exists) return;
          const newNames = [...selectedInvalidAreas, areaCode];
          setSelectedInvalidAreas(newNames);
          const nextArr = normaliseAreaArray(newNames);
          updateField('invalidDelivAreas')(nextArr);      // local + parent
        }}
        onDeleted={(areaCode: string) => {
          const newNames = selectedInvalidAreas.filter(
            (n) => String(n).toLowerCase() !== String(areaCode).toLowerCase()
          );
          setSelectedInvalidAreas(newNames);
          const nextArr = normaliseAreaArray(newNames);
          updateField('invalidDelivAreas')(nextArr);      // local + parent
        }}
      />
    </>
  );
};

export default EditReMailOptions;
