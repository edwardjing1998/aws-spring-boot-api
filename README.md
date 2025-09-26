// data.js
export const clientReportMappingData = {
  message: "ClientReportMapping has found records :10",
  response: {
    clientReportMapping: [
      { "reportClientId": "0001", "clientId": "0001", "sendToBothInd": false, "description": "Dialer-Xpedite do not delete" },
      { "reportClientId": "0002", "clientId": "0002", "sendToBothInd": false, "description": "Dialer-BCP  Dialer" },
      { "reportClientId": "wKq5", "clientId": "RRtQ", "sendToBothInd": true,  "description": "string" },
      { "reportClientId": "0006", "clientId": "0006", "sendToBothInd": false, "description": "Dialer-Pentagon Federal Credit Union" },
      { "reportClientId": "0007", "clientId": "0007", "sendToBothInd": false, "description": "Dialer-Pioneer  Credit Union" },
      { "reportClientId": "0010", "clientId": "0010", "sendToBothInd": false, "description": "Dialer-Chevron Texaco Federal CU" },
      { "reportClientId": "0017", "clientId": "0017", "sendToBothInd": false, "description": "Dialer-Premier America Credit Union" },
      { "reportClientId": "0207", "clientId": "0207", "sendToBothInd": false, "description": "Dialer-SunTrust BankCard, N.A. (Debit)" },
      { "reportClientId": "0221", "clientId": "0221", "sendToBothInd": false, "description": "Dialer-Household Bank/Metris" },
      { "reportClientId": "0246", "clientId": "0246", "sendToBothInd": false, "description": "Dialer-Alliance Funds" }
    ]
  }
}





import React, { useState, useMemo, useRef, useEffect } from 'react';
import { ModuleRegistry, AllCommunityModule } from 'ag-grid-community';
import { AgGridReact } from 'ag-grid-react';
import { Button } from '@mui/material';
import { CFormTextarea, CFormInput, CFormCheck, CFormSelect } from '@coreui/react';
import CustomSnackbar from '../../../components/CustomSnackbar';
import { EditIcon, ClientMappingDeleteIcon, SearchIcon } from '../../../assets/brand/svg-constants';
import { clientReportMappingData } from './data'; // ← import your JSON here
import '../../../scss/ClientReportMapping.scss';

ModuleRegistry.registerModules([AllCommunityModule]);

const ClientReportMapping = () => {
  const containerStyle = useMemo(() => ({ width: '100%', height: '89%' }), []);
  const gridStyle = useMemo(() => ({ height: '100%', width: '100%' }), []);
  const defaultColDef = useMemo(() => ({ flex: 1, minWidth: 100, sortable: true, filter: true }), []);

  const gridApi = useRef(null);

  // ✅ Use client-side rowData
  const [rowData, setRowData] = useState([]);

  useEffect(() => {
    const rows = clientReportMappingData?.response?.clientReportMapping ?? [];
    // give each row a stable id if you like
    setRowData(rows.map((r, i) => ({ id: i + 1, ...r })));
  }, []);

  // Columns tailored to your JSON fields
  const [columnDefs] = useState([
    { field: 'clientId', headerName: 'Client', maxWidth: 120 },
    { field: 'reportClientId', headerName: 'Report Client', maxWidth: 160 },
    {
      field: 'sendToBothInd',
      headerName: 'Send To Both',
      maxWidth: 140,
      valueGetter: (p) => (p.data?.sendToBothInd ? 'Yes' : 'No'),
    },
    { field: 'description', headerName: 'Description', minWidth: 200, flex: 2 },
    {
      headerName: '',
      field: 'actions',
      minWidth: 120,
      cellClass: 'actions-cell-flex-end',
      cellRenderer: (params) => (
        <div className="actions-cell icon-container action-cell-flex">
          <span className="icon-wrapper">
            <EditIcon style={{ cursor: 'pointer' }} onClick={() => handleEditClick(params.data)} />
          </span>
          <span className="icon-wrapper">
            <ClientMappingDeleteIcon
              style={{ cursor: 'pointer' }}
              onClick={() => handleDeleteClick(params.data)}
            />
          </span>
        </div>
      ),
    },
  ]);

  // (Optional) actions
  const [selectedRows, setSelectedRows] = useState([]);
  const [deleteSnackbarOpen, setDeleteSnackbarOpen] = useState(false);
  const [snackbarType, setSnackbarType] = useState('none');

  const [enableAddContent, setEnableAddContent] = useState(false);
  const [data, setData] = useState({
    clientId: '',
    reportClientId: '',
    sendToBothInd: false,
    application: '',
    description: '',
  });
  const [originalData, setOriginalData] = useState(null);
  const [searchValue, setSearchValue] = useState('');

  const applicationDropdownOptions = [
    { label: '', value: '' },
    { label: 'Dialer', value: 'Dialer' },
    { label: 'Rapid', value: 'Rapid' },
  ];

  const onHandleClear = () => {
    setOriginalData(null);
    setSelectedRows([]);
    setData({ clientId: '', reportClientId: '', sendToBothInd: false, application: '', description: '' });
  };

  const handleAddClick = () => {
    onHandleClear();
    setEnableAddContent(true);
  };

  const handleEditClick = (row) => {
    setEnableAddContent(true);
    // If you want to parse application/description from description string:
    const [application, ...rest] = (row.description || '').split('-');
    const form = {
      clientId: row.clientId,
      reportClientId: row.reportClientId,
      sendToBothInd: row.sendToBothInd,
      application: application || '',
      description: rest.join('-').trim() || row.description || '',
    };
    setData(form);
    setOriginalData(form);
  };

  const handleDeleteClick = (row) => {
    // with static data, just show a snackbar or remove locally
    setSnackbarType('delete-confirmation');
    // Example: remove from table locally
    setRowData((prev) => prev.filter((r) => !(r.clientId === row.clientId && r.reportClientId === row.reportClientId)));
  };

  const handleSave = () => {
    if (originalData) {
      // update
      setRowData((prev) =>
        prev.map((r) =>
          r.clientId === originalData.clientId && r.reportClientId === originalData.reportClientId
            ? { ...r, ...data, description: `${data.application ? data.application + '-' : ''}${data.description}` }
            : r
        )
      );
      setSnackbarType('update');
    } else {
      // add
      const newRow = {
        clientId: data.clientId,
        reportClientId: data.reportClientId,
        sendToBothInd: data.sendToBothInd,
        description: `${data.application ? data.application + '-' : ''}${data.description}`,
      };
      setRowData((prev) => [newRow, ...prev]);
      setSnackbarType('add');
    }
    setEnableAddContent(false);
  };

  const isDataChanged = () => {
    if (!originalData) {
      return (
        data.clientId.length === 4 &&
        data.reportClientId.length === 4 &&
        data.description.trim() !== ''
      );
    }
    return JSON.stringify(data) !== JSON.stringify(originalData);
  };

  const handleSearchChange = (value) => {
    setSearchValue(value);
    // quick client-side filter using grid API
    gridApi.current?.setQuickFilter(value);
  };

  return (
    <div className="client-report-mapping-page client-report-mapping-dialog">
      {/* Toolbar */}
      {!enableAddContent && (
        <div className="crm-toolbar action-container" style={{ padding: '12px 0' }}>
          <div className="search-input">
            <CFormInput
              placeholder="Search"
              value={searchValue}
              onChange={(e) => handleSearchChange(e.target.value)}
            />
            <span className="search-icon">
              <SearchIcon style={{ cursor: 'pointer' }} onClick={() => handleSearchChange(searchValue)} />
            </span>
          </div>
          <div>
            <Button variant="contained" size="small" onClick={handleAddClick}>
              Add
            </Button>
          </div>
        </div>
      )}

      {/* Content */}
      <div className="crm-content">
        {enableAddContent ? (
          <>
            <h2 style={{ margin: '0 0 12px' }}>Add / Edit Client Report Mapping</h2>
            <div className="add-main-container">
              <div className="row-container">
                <div className="clientId-container client-field">
                  <span className="label">Client ID</span>
                  <CFormInput
                    type="text"
                    maxLength={4}
                    value={data.clientId}
                    onChange={(e) => setData({ ...data, clientId: e.target.value })}
                    className="clientId-input"
                  />
                </div>
                <div className="clientId-container client-field">
                  <span className="label">Report Client ID</span>
                  <CFormInput
                    type="text"
                    maxLength={4}
                    value={data.reportClientId}
                    onChange={(e) => setData({ ...data, reportClientId: e.target.value })}
                    className="clientId-input"
                  />
                </div>
              </div>

              <div className="row-container">
                <div className="clientId-container client-field">
                  <span className="label">Application</span>
                  <CFormSelect
                    className="clientId-input"
                    value={data.application}
                    onChange={(e) => setData({ ...data, application: e.target.value })}
                  >
                    {applicationDropdownOptions.map((o) => (
                      <option key={o.value} value={o.value}>{o.label}</option>
                    ))}
                  </CFormSelect>
                </div>
                <div className="send-to-both-container">
                  <CFormCheck
                    className="send-to-both"
                    checked={data.sendToBothInd}
                    onChange={(e) => setData({ ...data, sendToBothInd: e.target.checked })}
                  />
                  <span>Send to Both</span>
                </div>
              </div>

              <div className="second-row-container">
                <div className="clientId-container">
                  <span>Description</span>
                  <CFormTextarea
                    value={data.description}
                    onChange={(e) => setData({ ...data, description: e.target.value })}
                    className="description-textarea"
                  />
                </div>
              </div>

              <div className="add-action-container client-report-mapping-button-container" style={{ marginTop: 12 }}>
                <Button variant="outlined" size="small" onClick={() => { setEnableAddContent(false); onHandleClear(); }}>
                  Cancel
                </Button>
                <Button variant="contained" size="small" disabled={!isDataChanged()} onClick={handleSave}>
                  Save
                </Button>
              </div>
            </div>
          </>
        ) : (
          <div style={containerStyle} className="ag-theme-quartz">
            <div style={gridStyle}>
              <AgGridReact
                rowData={rowData}                 {/* ← use static JSON */}
                columnDefs={columnDefs}
                defaultColDef={defaultColDef}
                pagination={true}
                paginationPageSize={10}
                paginationPageSizeSelector={[10, 20, 50, 100]}
                rowSelection="multiple"
                onGridReady={(params) => { gridApi.current = params.api; }}
                onSelectionChanged={(p) => setSelectedRows(p.api.getSelectedRows())}
              />
            </div>
          </div>
        )}
      </div>

      {/* Snackbars */}
      <CustomSnackbar
        type={snackbarType}
        open={snackbarType !== 'none'}
        handleOk={() => setSnackbarType('none')}
        onClose={() => setSnackbarType('none')}
        title={snackbarType === 'add' || snackbarType === 'update' || snackbarType === 'delete-confirmation' ? 'Client Report Mapping' : ''}
        body={
          snackbarType === 'add' ? 'You have successfully Added a new client'
          : snackbarType === 'update' ? 'You have successfully Updated a client'
          : snackbarType === 'delete-confirmation' ? 'You have successfully Deleted a client'
          : ''
        }
      />
    </div>
  );
};

export default ClientReportMapping;
