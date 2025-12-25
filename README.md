import React, { useEffect, useMemo, useState } from 'react';
import { Typography, Button, IconButton, Tooltip } from '@mui/material';
import InfoOutlinedIcon from '@mui/icons-material/InfoOutlined';
import EditOutlinedIcon from '@mui/icons-material/EditOutlined';
import DeleteOutlineIcon from '@mui/icons-material/DeleteOutline';
import { CCard, CCardBody } from '@coreui/react';

import ClientReportWindow from './ClientReportWindow';
import { ClientReportRow, EditClientReportProps } from './EditClientReport.types';
import { editRowCellStyle, editHeaderCellStyle } from './EditClientReport.styles';
import { fetchEditClientReport } from './ClientReport.service';

const PAGE_SIZE = 8;

const EditClientReport: React.FC<EditClientReportProps> = ({ selectedGroupRow, isEditable, onDataChange }) => {
  const [tableData, setTableData] = useState<ClientReportRow[]>([]);
  const [page, setPage] = useState<number>(0);

  const [refreshKey, setRefreshKey] = useState<number>(0);
  const [localCountAdjustment, setLocalCountAdjustment] = useState<number>(0);

  const clientId =
    selectedGroupRow?.client ||
    selectedGroupRow?.clientId ||
    selectedGroupRow?.billingSp ||
    '';

  const baseTotalCount = selectedGroupRow?.reportOptionTotal || 0;
  const totalCount = Math.max(0, baseTotalCount + localCountAdjustment);

  useEffect(() => {
    setLocalCountAdjustment(0);
  }, [baseTotalCount, clientId]);

  const [modalOpen, setModalOpen] = useState<boolean>(false);
  const [modalMode, setModalMode] = useState<'detail' | 'edit' | 'new' | 'delete'>('detail');
  const [modalRowIdx, setModalRowIdx] = useState<number | null>(null);
  const [draftRow, setDraftRow] = useState<ClientReportRow | null>(null);

  useEffect(() => {
    if (!clientId) return;

    const fetchData = async () => {
      try {
        const nextRows = await fetchEditClientReport(clientId, page, PAGE_SIZE);
        setTableData(nextRows);
      } catch (error) {
        console.error("Error fetching client reports:", error);
        setTableData([]);
      }
    };

    fetchData();
  }, [clientId, page, refreshKey]);

  const pageCount = Math.ceil(totalCount / PAGE_SIZE);
  const pageData = tableData;

  // ✅ Helper: tell parent the new total (so pagination stays correct after add/delete)
  const notifyParentTotal = (newTotal: number) => {
    if (!clientId) return;

    onDataChange?.({
      clientId,
      reportOptionTotal: Math.max(0, newTotal),
    } as any);
  };

  const labelCell = (text: string, align: 'left' | 'center' | 'right' = 'center') => (
    <div style={{ ...editRowCellStyle, justifyContent: align }}>
      <Typography noWrap sx={{ fontSize: '0.72rem', lineHeight: 1.1 }}>
        {text}
      </Typography>
    </div>
  );

  const mapReceive = (v: string) => (v === '1' ? 'Yes' : v === '2' ? 'No' : 'None');
  const mapDestination = (v: string) => (v === '1' ? 'File' : v === '2' ? 'Print' : 'None');

  const openDetail = (rowIdx: number) => {
    setModalMode('detail');
    setModalRowIdx(rowIdx);
    setDraftRow(null);
    setModalOpen(true);
  };
  const openEdit = (rowIdx: number) => {
    setModalMode('edit');
    setModalRowIdx(rowIdx);
    setDraftRow(null);
    setModalOpen(true);
  };
  const openDelete = (rowIdx: number) => {
    setModalMode('delete');
    setModalRowIdx(rowIdx);
    setDraftRow(null);
    setModalOpen(true);
  };

  // ✅ Delete: update local + parent total + page index safely
  const handleRemove = (rowIdx: number) => {
    const nextTotal = Math.max(0, totalCount - 1);

    // optimistic total update
    setLocalCountAdjustment(prev => prev - 1);

    // ✅ update parent immediately so pageCount becomes correct
    notifyParentTotal(nextTotal);

    // ✅ if we deleted the last item on the last page, move back one page
    const nextPageCount = Math.ceil(nextTotal / PAGE_SIZE); // could be 0
    const maxPageIdx = Math.max(0, nextPageCount - 1);

    setPage((p) => Math.min(p, maxPageIdx));

    // refresh current page data from server
    setRefreshKey(prev => prev + 1);
  };

  const handleNewClick = () => {
    if (!isEditable) return;
    setModalMode('new');
    setModalRowIdx(null);
    setDraftRow({
      reportName: '',
      reportId: null,
      receive: '0',
      destination: '0',
      fileText: '0',
      email: '0',
      password: '',
      emailBodyTx: '',
      fileExt: '',
    });
    setModalOpen(true);
  };

  // ✅ Add: update local + parent total (and keep your current paging behavior)
  const handleSaveFromModal = (updatedRow: Partial<ClientReportRow>) => {
    if (modalRowIdx == null) {
      const normalized: ClientReportRow = {
        reportName: updatedRow?.reportName ?? '',
        reportId: updatedRow?.reportId ?? null,
        receive: updatedRow?.receive != null ? String(updatedRow.receive) : '0',
        destination: updatedRow?.destination != null ? String(updatedRow.destination) : '0',
        fileText: updatedRow?.fileText != null ? String(updatedRow.fileText) : '0',
        email: updatedRow?.email != null ? String(updatedRow.email) : '0',
        password: updatedRow?.password ?? '',
        emailBodyTx: updatedRow?.emailBodyTx ?? '',
        fileExt: updatedRow?.fileExt ?? '',
      };

      const nextTotal = totalCount + 1;

      // optimistic total update
      setLocalCountAdjustment(prev => prev + 1);

      // ✅ update parent immediately so pageCount becomes correct
      notifyParentTotal(nextTotal);

      const currentLastPageIdx = Math.max(0, Math.ceil(totalCount / PAGE_SIZE) - 1);
      const isLastPage = page === currentLastPageIdx;

      if (isLastPage) {
        if (tableData.length < PAGE_SIZE) {
          setTableData(prev => [...prev, normalized]);
        } else {
          setPage(page + 1);
          setTableData([normalized]);
        }
      } else {
        // not on last page, keep view
      }

      // keep dialog open
    } else {
      // edit existing row -> refresh
      setRefreshKey(prev => prev + 1);
    }
  };

  const handleDeleteFromModal = () => {
    if (modalRowIdx == null) return;
    const snapshot = tableData[modalRowIdx] || null;
    setDraftRow(snapshot);
    setModalRowIdx(null);
    handleRemove(modalRowIdx);
  };

  const currentRow = modalRowIdx == null ? draftRow : tableData[modalRowIdx] || null;

  return (
    <div style={{ padding: '10px' }}>
      <CCard>
        <CCardBody>
          <div style={{ height: '280px', marginBottom: '4px', marginTop: '2px' }}>
            <div
              style={{
                display: 'grid',
                gridTemplateColumns: '4fr 1fr 1fr 3fr',
                columnGap: '4px',
              }}
            >
              {['Report Name', 'Receive', 'Destination', 'Action'].map((header) => (
                <div
                  key={header}
                  style={{
                    ...editHeaderCellStyle,
                    textAlign: header === 'Action' ? 'center' : 'left',
                  }}
                >
                  {header}
                </div>
              ))}

              {pageData.map((item, index) => {
                const rowIdx = index;
                return (
                  <React.Fragment key={`${item.reportId}-${rowIdx}`}>
                    <div style={editRowCellStyle}>{item.reportName}</div>
                    {labelCell(mapReceive(item.receive))}
                    {labelCell(mapDestination(item.destination))}
                    <div style={{ ...editRowCellStyle, gap: 4, justifyContent: 'center' }}>
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
                            onClick={() => openDelete(rowIdx)}
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
              disabled={page === 0}
            >
              ◀ Previous
            </Button>
            <Typography fontSize="0.72rem">
              Page {totalCount > 0 ? page + 1 : 0} of {pageCount}
            </Typography>
            <Button
              variant="text"
              size="small"
              sx={{ fontSize: '0.68rem', padding: '2px 6px', minWidth: 'unset', textTransform: 'none' }}
              onClick={() => setPage((p) => Math.min(p + 1, pageCount - 1))}
              disabled={page >= pageCount - 1}
            >
              Next ▶
            </Button>
          </div>

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

      <ClientReportWindow
        open={modalOpen}
        mode={modalMode}
        row={currentRow}
        clientId={clientId}
        onClose={() => {
          setModalOpen(false);
          setDraftRow(null);
        }}
        onSave={handleSaveFromModal}
        onDelete={handleDeleteFromModal}
      />
    </div>
  );
};

export default EditClientReport;



// --- Interfaces ---

// Represents the data structure used for rows in the table
export interface ClientReportRow {
  reportName: string;
  reportId: number | null;
  receive: string;      // '0'|'1'|'2'
  destination: string;  // '0'|'1'|'2'
  fileText: string;     // '0'|'1'|'2'
  email: string;        // '0'|'1'|'2'
  password: string;
  emailBodyTx: string;
  fileExt: string;
}

// Represents the raw data coming from the API or parent props
export interface ClientReportItem {
  reportId?: number;
  receiveFlag?: boolean | number;
  outputTypeCd?: number;
  fileTypeCd?: number;
  emailFlag?: number;
  reportPasswordTx?: string;
  emailBodyTx?: string;
  fileExt?: string;
  reportDetails?: {
    queryName?: string;
    reportId?: number;
    fileExt?: string;
    [key: string]: any;
  };
  [key: string]: any;
}

export interface EditClientReportProps {
  selectedGroupRow: {
    client?: string;
    clientId?: string;
    billingSp?: string;
    reportOptionTotal?: number;
    reportOptions?: ClientReportItem[];
    [key: string]: any;
  } | null;
  isEditable: boolean;
  onDataChange?: (updatedGroupRow: any) => void;
}


// Interface for component props
export interface PreviewClientReportsProps {
  clientId: string;
  data: ClientReportItem[];
  reportOptionTotal?: number;
}






import { Button, Typography } from '@mui/material';
import React, { useState, useEffect } from 'react';

import {
  previewCellStyle,
  previewHeaderStyle
} from './EditClientReport.styles';

import {
  ClientReportItem,
  PreviewClientReportsProps
} from './EditClientReport.types';

import { fetchPreviewClientReports } from './ClientReport.service';

const PAGE_SIZE = 8; // Match your API call size
const COLUMNS = 2;

const PreviewClientReports: React.FC<PreviewClientReportsProps> = ({
  clientId,
  data,
  reportOptionTotal
}) => {
  // Store page index per client: { "0042": 1, "0043": 0 }
  const [pageMap, setPageMap] = useState<Record<string, number>>({});
  const [reports, setReports] = useState<ClientReportItem[]>(data || []);

  // Get current page for this client, default to 0
  const page = clientId ? (pageMap[clientId] || 0) : 0;

  // Total elements used for pagination
  const totalCount = reportOptionTotal;

  // Helper to update page for specific client
  const setClientPage = (newPage: number) => {
    if (!clientId) return;
    setPageMap((prev) => ({
      ...prev,
      [clientId]: newPage,
    }));
  };

  // Keep local reports in sync when parent provides data for the current client/page (optional optimization)
  useEffect(() => {
    if (data) setReports(data);
  }, [data]);

  // Fetch current page whenever page/clientId changes
  useEffect(() => {
    if (!clientId) return;

    const fetchData = async () => {
      try {
        const result = await fetchPreviewClientReports(clientId, page, PAGE_SIZE);
        setReports(result);
      } catch (e) {
        console.error('fetchPreviewClientReports failed:', e);
        setReports([]);
      }
    };

    fetchData();
  }, [clientId, page]);

  const hasData = reports && reports.length > 0;

  // Calculate total pages
  const pageCount = totalCount ? Math.ceil(totalCount / PAGE_SIZE) : 0;

  return (
    <div style={{ display: 'flex', flexDirection: 'column', height: '100%' }}>
      {/* Grid Table */}
      <div
        style={{
          flex: 1,
          display: 'grid',
          gridTemplateColumns: '410px 120px 120px 120px',
          rowGap: '0px',
          columnGap: '4px',
          minHeight: '100px',
          alignContent: 'start',
        }}
      >
        {/* Header Row */}
        <div style={previewHeaderStyle}>Name</div>
        <div style={previewHeaderStyle}>Received</div>
        <div style={previewHeaderStyle}>Type</div>
        <div style={previewHeaderStyle}>Output</div>

        {/* Data Rows */}
        {hasData ? (
          reports.map((item, index) => (
            <React.Fragment key={`${item.reportId}-${index}`}>
              <div style={previewCellStyle}>{item.reportDetails?.queryName?.trim() || ''}</div>
              <div style={previewCellStyle}>{item.receiveFlag ? 'Yes' : 'No'}</div>
              <div style={previewCellStyle}>{item.reportDetails?.fileExt || ''}</div>
              <div style={previewCellStyle}>{item.outputTypeCd}</div>
            </React.Fragment>
          ))
        ) : (
          <Typography sx={{ gridColumn: `span ${COLUMNS}`, fontSize: '0.75rem', padding: '0 16px' }}>
            xxxx - xxxx
          </Typography>
        )}
      </div>

      {/* Pagination */}
      <div
        style={{
          marginTop: '16px',
          display: 'flex',
          justifyContent: 'space-between',
          alignItems: 'center',
        }}
      >
        <Button
          variant="text"
          size="small"
          sx={{ fontSize: '0.7rem', padding: '2px 8px', minWidth: 'unset', textTransform: 'none' }}
          onClick={() => setClientPage(Math.max(page - 1, 0))}
          disabled={page === 0}
        >
          ◀ Previous
        </Button>

        <Typography fontSize="0.75rem">
          Page {totalCount ? page + 1 : 0} {totalCount !== undefined ? `of ${pageCount}` : ''}
        </Typography>

        <Button
          variant="text"
          size="small"
          sx={{ fontSize: '0.7rem', padding: '2px 8px', minWidth: 'unset', textTransform: 'none' }}
          onClick={() => setClientPage(page + 1)}
          disabled={totalCount !== undefined ? page >= pageCount - 1 : reports.length < PAGE_SIZE}
        >
          Next ▶
        </Button>
      </div>
    </div>
  );
};

export default PreviewClientReports;




import React, { useState } from 'react';
import { CRow, CCol, CCard, CCardBody } from '@coreui/react';
import { Tabs, Tab, Box, TextField, FormControl, FormControlLabel, Checkbox } from '@mui/material';
import PreviewClientEmails from '../emails/PreviewClientEmails';
import PreviewAtmAndCashPrefix from '../atm-cash-prefix/PreviewAtmAndCashPrefix';
import PreviewClientReports from '../reports/PreviewClientReports';
import PreviewClientSysPrinList from './PreviewClientSysPrinList';
import { REPORT_BREAK_OPTIONS, SEARCH_TYPE_OPTIONS } from '../utils/ClientInfoFieldValueMapping';

// --- Interfaces ---

export interface ClientGroupRow {
  client?: string;
  name?: string;
  addr?: string;
  city?: string;
  state?: string;
  zip?: string;
  contact?: string;
  phone?: string;
  phoneCountryCode?: string;
  active?: boolean;
  faxNumber?: string;
  billingSp?: string;
  reportBreakFlag?: string | number;
  chLookUpType?: string | number;
  excludeFromReport?: boolean;
  positiveReports?: boolean;
  subClientInd?: boolean;
  subClientXref?: string;
  amexIssued?: boolean;
  sysPrins?: any[]; 
  sysPrinTotal?: number;
  reportOptions?: any[]; 
  reportOptionTotal?: number;
  sysPrinsPrefixes?: any[]; 
  clientPrefixTotal?: number;
  clientEmail?: any[];
  clientEmailTotal?: number;
  [key: string]: any;
}

interface PreviewClientInformationProps {
  // ✅ FIX: Update type to match the state setter from ClientInformationPage
  setClientInformationWindow?: React.Dispatch<React.SetStateAction<{
    open: boolean;
    mode: 'edit' | 'new' | 'delete';
  }>>;
  selectedGroupRow: ClientGroupRow | null;
}

const PreviewClientInformation: React.FC<PreviewClientInformationProps> = ({
  setClientInformationWindow,
  selectedGroupRow,
}) => {
  // eslint-disable-next-line @typescript-eslint/no-unused-vars
  const [isEditable] = useState<boolean>(false);
  const [tabIndex, setTabIndex] = useState<number>(0);

    const clientId =
    selectedGroupRow?.client ||
    (selectedGroupRow as any)?.clientId ||
    selectedGroupRow?.billingSp ||
    '';

  const handleTabChange = (_event: React.SyntheticEvent, newValue: number) => {
    setTabIndex(newValue);
  };

  const sharedSx = {
    '& .MuiInputBase-root': {
      height: '38px',       // outer container
      fontSize: '0.78rem',
    },
    '& .MuiInputBase-input': {
      padding: '8px 10px',  // more padding for taller field
      height: '22px',       // input line height
      fontSize: '0.78rem',
      lineHeight: '1.2rem',
    },
    '& .MuiInputLabel-root': {
      fontSize: '0.78rem',
      lineHeight: '1.2rem',
    },
    '& .MuiInputBase-input.Mui-disabled': {
      color: 'black',
      WebkitTextFillColor: 'black',
    },
    '& .MuiInputLabel-root.Mui-disabled': {
      color: 'black',
    },
  };
  
  return (
    <>
      <Box sx={{ borderBottom: 1, borderColor: 'divider', marginBottom: '10px' }}>
        <Tabs value={tabIndex} onChange={handleTabChange} variant="scrollable" scrollButtons="auto">
          <Tab label="General Info" sx={{ textTransform: 'none' }} />
          <Tab label="Sys/Prin List" sx={{ textTransform: 'none' }} />
          <Tab label="Client Reports" sx={{ textTransform: 'none' }} />
          <Tab label="ATM Prefixes" sx={{ textTransform: 'none' }} />
          <Tab label="Client Emails" sx={{ textTransform: 'none' }} />
        </Tabs>
      </Box>

      {tabIndex === 0 && (
        <>
          <CCard style={{ height: '70px', marginBottom: '4px', marginTop: '15px', boxShadow: 'none', border: 'none', backgroundColor: 'white' }}>
            <CCardBody
              style={{
                padding: '0.25rem 0.5rem',
                height: '100%',
                backgroundColor: 'white',
                display: 'flex',
                flexDirection: 'column',
                justifyContent: 'space-between',
              }}
            >
            <CRow style={{ height: '35px' }}>
              <CCol style={{ flex: '0 0 25%' }}>
                <p style={{ margin: 0, fontSize: '0.78rem' }}>Phone</p>
              </CCol>
              <CCol style={{ flex: '0 0 25%' }}>
                <p style={{ margin: 0, fontSize: '0.78rem'  }}>Fax Number</p>
              </CCol>
              <CCol style={{ flex: '0 0 25%' }}>
                <p style={{ margin: 0, fontSize: '0.78rem' }}>Contact</p>
              </CCol>

              <CCol style={{ flex: '0 0 3%' }}>
              </CCol>

              {/* Address column, right-aligned */}
              <CCol
                style={{
                  textAlign: 'left', 
                  display: 'flex',
                  flexDirection: 'column',
                  justifyContent: 'center',
                  flex: '0 0 20%' 
                }}
              >
                {/* Address row – allow wrapping */}
                <CRow style={{ minHeight: '20px' }}>
                  <CCol
                    style={{
                      flex: '0 0 100%',
                      fontSize: '0.78rem',
                      textAlign: 'left',
                      whiteSpace: 'normal',      // allow wrapping
                      wordBreak: 'break-word',   // break long words if needed
                      lineHeight: '1.1rem',
                      color: '#2554C7'
                    }}
                  >
                    {selectedGroupRow?.addr || 'xxx'}
                  </CCol>
                </CRow>

                {/* City / State row – can be fixed height or also minHeight if you want wrapping */}
                <CRow style={{ height: '20px' }}>
                  <CCol
                    style={{
                      flex: '0 0 100%',
                      fontSize: '0.78rem',
                      textAlign: 'left',
                      whiteSpace: 'nowrap',      // keep on one line (optional)
                      overflow: 'hidden',
                      textOverflow: 'ellipsis',
                      color: '#2554C7'
                    }}
                  >
                    {selectedGroupRow?.city || 'xxx'}, {selectedGroupRow?.state || 'xx'}
                  </CCol>
                </CRow>

                {/* Zip row with bottom border – stays properly aligned */}
                <CRow style={{ height: '20px', borderBottom: '1px solid #2554C7' }}>
                  <CCol
                    style={{
                      flex: '0 0 100%',
                      fontSize: '0.78rem',
                      textAlign: 'left',
                      color: '#2554C7'
                    }}
                  >
                    {selectedGroupRow?.zip || 'xxx'}
                  </CCol>
                </CRow>
              </CCol>
              <CCol style={{ flex: '0 0 2%', fontSize: '0.78rem', textAlign: 'left' }}>
              </CCol>
            </CRow>

              <CRow style={{ height: '35px' }}>
                <CCol>
                  <TextField placeholder="xxx" value={selectedGroupRow?.phone || 'xxx'} size="small" fullWidth disabled={!isEditable} sx={sharedSx} />
                </CCol>
                <CCol>
                   <TextField placeholder="xxx" value={selectedGroupRow?.faxNumber || 'xxx'} size="small" fullWidth disabled={!isEditable} sx={sharedSx} />
                </CCol>
                <CCol>
                   <TextField placeholder="xxx" value={selectedGroupRow?.contact || 'xxx'} size="small" fullWidth disabled={!isEditable} sx={sharedSx} />
                </CCol>
                <CCol>
                </CCol>
              </CRow>
            </CCardBody>
          </CCard>

          <CCard style={{ height: '70px', marginBottom: '4px', marginTop: '15px', boxShadow: 'none', border: 'none', backgroundColor: 'white' }}>
            <CCardBody
              style={{
                padding: '0.25rem 0.5rem',
                height: '100%',
                backgroundColor: 'white',
                display: 'flex',
                flexDirection: 'column',
                justifyContent: 'space-between',
              }}
            >
              <CRow style={{ height: '35px' }}>
                <CCol><p style={{ margin: 0, fontSize: '0.78rem' }}>Billing Sys/Prin</p></CCol>
                <CCol><p style={{ margin: 0, fontSize: '0.78rem' }}>Report Breaks</p></CCol>
                <CCol><p style={{ margin: 0, fontSize: '0.78rem' }}>Search Type</p></CCol>
                <CCol><p style={{ margin: 0, fontSize: '0.78rem' }}>Client ID XRef</p></CCol>
              </CRow>

              <CRow style={{ height: '35px' }}>
                <CCol>
                  <TextField placeholder="xxx" value={selectedGroupRow?.billingSp || ''} size="small" fullWidth disabled={!isEditable} sx={sharedSx} />
                </CCol>
                <CCol>
                  <TextField
                    placeholder="xxx"
                    value={REPORT_BREAK_OPTIONS.find(opt => opt.value === String(selectedGroupRow?.reportBreakFlag ?? ''))?.label || ''}
                    size="small"
                    fullWidth
                    disabled={!isEditable}
                    sx={sharedSx}
                  />
                </CCol>
                <CCol>
                  <TextField
                    placeholder="xxx"
                    value={SEARCH_TYPE_OPTIONS.find(opt => opt.value === String(selectedGroupRow?.chLookUpType ?? ''))?.label || ''}
                    size="small"
                    fullWidth
                    disabled={!isEditable}
                    sx={sharedSx}
                  />
                </CCol>
                <CCol>
                  <TextField placeholder="xxx" value={selectedGroupRow?.subClientXref || ''} size="small" fullWidth disabled={!isEditable} sx={sharedSx} />
                </CCol>
              </CRow>
            </CCardBody>
          </CCard>

          <CCard style={{ marginTop: '25px', marginBottom: '10px' }}>
            <CCardBody
              style={{
                padding: '0.8rem',
                backgroundColor: 'white',
                display: 'flex',
                flexDirection: 'column',
                rowGap: '0px',
              }}
            >
              <CRow style={{ height: '35px' }}>
                <CCol style={{ flex: '0 0 40%' }}>
                  <FormControlLabel
                    control={<Checkbox size="small" checked={selectedGroupRow?.active || false} disabled={!isEditable} />}
                    label="Client Active"
                    sx={{ pl: 1, m: 0, '& .MuiFormControlLabel-label': { fontSize: '0.78rem', color: 'black' } }}
                  />
                </CCol>
                <CCol style={{ flex: '0 0 40%' }}>
                  <FormControlLabel
                    control={<Checkbox size="small" checked={selectedGroupRow?.positiveReports || false} disabled={!isEditable} />}
                    label="Positive Reporting"
                    sx={{ pl: 1, m: 0, '& .MuiFormControlLabel-label': { fontSize: '0.78rem', color: 'gray' } }}
                  />
                </CCol>
                <CCol style={{ flex: '0 0 20%', fontSize: '0.78rem' }}>
                </CCol>
              </CRow>

              <CRow style={{ height: '35px' }}>

                <CCol style={{ flex: '0 0 40%' }}>
                  <FormControlLabel
                    control={<Checkbox size="small" checked={selectedGroupRow?.excludeFromReport || false} disabled={!isEditable} />}
                    label="Exclude From Postage Reports"
                    sx={{ pl: 1, m: 0, '& .MuiFormControlLabel-label': { fontSize: '0.78rem', color: 'black' } }}
                  />
                </CCol>

                <CCol style={{ flex: '0 0 40%' }}>
                  <FormControlLabel
                    control={<Checkbox size="small" checked={selectedGroupRow?.subClientInd || false} disabled={!isEditable} />}
                    label="Sub Client"
                    sx={{ pl: 1, m: 0, '& .MuiFormControlLabel-label': { fontSize: '0.78rem', color: 'black' } }}
                  />
                </CCol>
              </CRow>

              <CRow style={{ height: '35px' }}>
                <CCol style={{ flex: '0 0 40%' }}>
                  <FormControlLabel
                    control={<Checkbox size="small" checked={selectedGroupRow?.amexIssued || false} disabled={!isEditable} />}
                    label="Check Here: If American Express Issued"
                    sx={{ pl: 1, m: 0, '& .MuiFormControlLabel-label': { fontSize: '0.78rem', color: 'black' } }}
                  />
                </CCol>
                <CCol style={{ flex: '0 0 30%' }}>
                </CCol>
              </CRow>
            </CCardBody>
          </CCard>
        </>
      )}

      {tabIndex === 1 && (
        <>
         <CCard
            style={{
              height: '35px',
              marginBottom: '4px',
              marginTop: '2px',
              border: 'none',                   // remove border
              backgroundColor: '#f3f6f8',       // very light green background
              boxShadow: 'none',                // remove any shadow
              borderRadius: '4px'               // optional: slight rounding
            }}
          >
            <CCardBody
              className="d-flex align-items-center"
              style={{
                padding: '0.25rem 0.5rem',
                height: '100%',
                backgroundColor: 'transparent' // ensure it inherits from card
              }}
            >
              <p
                style={{
                  margin: 0,
                  fontSize: '0.78rem',
                  fontWeight: '500'
                }}
              >
                {selectedGroupRow
                  ? `Selected Client: ${selectedGroupRow.client} - ${selectedGroupRow.name || ''}`
                  : 'No Client Found'}
              </p>
            </CCardBody>
          </CCard>

          <CCard style={{ height: '250px', marginBottom: '4px' }}>
            <CCardBody style={{ padding: '0.25rem 0.5rem' }}>
              <PreviewClientSysPrinList 
              data={selectedGroupRow?.sysPrins || []} 
              sysPrinTotal={selectedGroupRow?.sysPrinTotal} />
            </CCardBody>
          </CCard>
        </>
      )}

      {tabIndex === 2 && (
        <>
         <CCard
            style={{
              height: '35px',
              marginBottom: '4px',
              marginTop: '2px',
              border: 'none',                   // remove border
              backgroundColor: '#f3f6f8',       // very light green background
              boxShadow: 'none',                // remove any shadow
              borderRadius: '4px'               // optional: slight rounding
            }}
          >
            <CCardBody
              className="d-flex align-items-center"
              style={{
                padding: '0.25rem 0.5rem',
                height: '100%',
                backgroundColor: 'transparent' // ensure it inherits from card
              }}
            >
              <p
                style={{
                  margin: 0,
                  fontSize: '0.78rem',
                  fontWeight: '500'
                }}
              >
                {selectedGroupRow
                  ? `Selected Client: ${selectedGroupRow.client} - ${selectedGroupRow.name || ''}`
                  : 'No Client Found'}
              </p>
            </CCardBody>
          </CCard>

          <CCard style={{ height: '250px', marginBottom: '4px' }}>
            <CCardBody style={{ padding: '0.25rem 0.5rem' }}>
              <PreviewClientReports
                clientId={clientId} 
                data={selectedGroupRow?.reportOptions || []} 
                reportOptionTotal={selectedGroupRow?.reportOptionTotal} 
              />
            </CCardBody>
          </CCard>
        </>
      )}

      {tabIndex === 3 && (
        <>
         <CCard
            style={{
              height: '35px',
              marginBottom: '4px',
              marginTop: '2px',
              border: 'none',                   // remove border
              backgroundColor: '#f3f6f8',       // very light green background
              boxShadow: 'none',                // remove any shadow
              borderRadius: '4px'               // optional: slight rounding
            }}
          >
            <CCardBody
              className="d-flex align-items-center"
              style={{
                padding: '0.25rem 0.5rem',
                height: '100%',
                backgroundColor: 'transparent' // ensure it inherits from card
              }}
            >
              <p
                style={{
                  margin: 0,
                  fontSize: '0.78rem',
                  fontWeight: '500'
                }}
              >
                {selectedGroupRow
                  ? `Selected Client: ${selectedGroupRow.client} - ${selectedGroupRow.name || ''}`
                  : 'No Client Found'}
              </p>
            </CCardBody>
          </CCard>

          <CCard style={{ height: '250px', marginBottom: '4px' }}>
            <CCardBody style={{ padding: '0.25rem 0.5rem' }}>
              <PreviewAtmAndCashPrefix 
                data={selectedGroupRow?.sysPrinsPrefixes || []} 
                clientPrefixTotal={selectedGroupRow?.clientPrefixTotal} />
            </CCardBody>
          </CCard>
        </>
      )}

      {tabIndex === 4 && (
        <>
         <CCard
            style={{
              height: '35px',
              marginBottom: '4px',
              marginTop: '2px',
              border: 'none',                   // remove border
              backgroundColor: '#f3f6f8',       // very light green background
              boxShadow: 'none',                // remove any shadow
              borderRadius: '4px'               // optional: slight rounding
            }}
          >
            <CCardBody
              className="d-flex align-items-center"
              style={{
                padding: '0.25rem 0.5rem',
                height: '100%',
                backgroundColor: 'transparent' // ensure it inherits from card
              }}
            >
              <p
                style={{
                  margin: 0,
                  fontSize: '0.78rem',
                  fontWeight: '500'
                }}
              >
                {selectedGroupRow
                  ? `Selected Client: ${selectedGroupRow.client} - ${selectedGroupRow.name || ''}`
                  : 'No Client Found'}
              </p>
            </CCardBody>
          </CCard>

          <CCard style={{ height: '250px', marginBottom: '4px' }}>
            <CCardBody style={{ padding: '0.25rem 0.5rem' }}>
              <PreviewClientEmails 
                data={selectedGroupRow?.clientEmail || []} 
                clientEmailTotal={selectedGroupRow?.clientEmailTotal} />
            </CCardBody>
          </CCard>
        </>
      )}
    </>
  );
};

export default PreviewClientInformation;



