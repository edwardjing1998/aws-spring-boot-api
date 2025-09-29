import { useEffect, useRef, useState, useMemo } from 'react';
import {
  CButton, CFormInput, CCol, CFormCheck, CRow, CCard, CFormSelect, CCardBody
} from '@coreui/react';
import './WebClientDirectory.scss';

import { AllCommunityModule, ModuleRegistry } from 'ag-grid-community';
import { AgGridReact } from 'ag-grid-react';
import { webClientDirectoryRows } from './data'; // ← import local JSON

ModuleRegistry.registerModules([AllCommunityModule]);

const defaultColDef = {
  flex: 1,
  filter: true,
  floatingFilter: false,
  sortable: true,
  resizable: true,
};

const initialRef = {
  sUserId: '',
  sClientId: '',
  sWebDirectoryPath: '',
  iWebReportid: '',
};

const WebClientDirectory = () => {
  const [reportIdList, setReportIdList] = useState([]);
  const [rowData, setRowData] = useState([]);

  const [data, setData] = useState({
    userId: '',
    clientId: '',
    pathTx: '',
    webReportId: '',
    administrator: false,
  });
  const [ref, setRef] = useState(initialRef);
  const [selectedReportName, setSelectedReportName] = useState('');
  const [successMessage, setSuccessMessage] = useState('');
  const [validationError, setValidationError] = useState('');
  const [pageSize, setPageSize] = useState(10);

  const gridApi = useRef(null);

  // Init from local JSON
  useEffect(() => {
    // Normalize/shape the local rows
    const normalized = (webClientDirectoryRows || []).map((r) => {
      const userId = (r.userId ?? '').trimEnd();        // keep left padding if any, but drop trailing spaces
      const clientId = (r.clientId ?? '').trim();       // client can be blank or 4 chars
      const pathTx = r.pathTx ?? '';
      const webReportId = Number(r.webReportId) || 0;

      return {
        userId,
        clientId,
        pathTx,
        webReportIdStr: String(webReportId).padStart(5, '0'), // for display
        webReportId,                                          // for logic
      };
    });

    setRowData(normalized);

    // Build Report ID dropdown (unique, sorted)
    const uniqueIds = Array.from(new Set(normalized.map((r) => r.webReportId))).sort((a, b) => a - b);
    const dropdown = [{} , ...uniqueIds.map((id) => ({
      webReportId: id,
      reportName: '' // no reportName in JSON, keep blank
    }))];
    setReportIdList(dropdown);
  }, []);

  const handleTableRowClick = (item) => {
    setTimeout(() => {
      setData({
        userId: item.userId ?? '',
        clientId: item.clientId ?? '',
        pathTx: item.pathTx ?? '',
        webReportId: String(item.webReportId ?? ''),
        administrator: (item.clientId ?? '').trim() === '' ? true : false,
      });
      setRef({
        sUserId: item.userId ?? '',
        sClientId: item.clientId ?? '',
        sWebDirectoryPath: item.pathTx ?? '',
        iWebReportid: String(item.webReportId ?? ''),
      });
      setSelectedReportName(
        reportIdList.find((r) => r.webReportId === item.webReportId)?.reportName || ''
      );
    }, 0);
  };

  const isDeleteEnabled =
    data.userId === ref.sUserId &&
    data.clientId === ref.sClientId &&
    ref.sUserId !== '';

  const isReportIdSelectDisabled =
    data.userId === ref.sUserId &&
    data.clientId === ref.sClientId &&
    ref.sUserId !== '';

  const isAdminUserEnabled =
    (data.userId.trim() !== '' && data.clientId.trim() === '') ||
    (data.userId.trim() === '' && data.clientId.trim() === '');

  const isClientIdDisabled = data.administrator === true;

  useEffect(() => {
    if (data.clientId.trim() === '' && !data.administrator) {
      setData((prev) => ({ ...prev, administrator: false }));
    }
  }, [data.clientId]);

  const isChanged = () => {
    if (
      data.userId.trim() === ref.sUserId.trim() &&
      data.clientId.trim() === ref.sClientId.trim() &&
      data.pathTx.trim() === ref.sWebDirectoryPath.trim() &&
      String(data.webReportId) === String(ref.iWebReportid)
    ) {
      return false;
    }
    if (
      data.userId.trim().length > 0 &&
      data.pathTx.trim().length > 0 &&
      data.clientId.trim().length === 4 &&
      data.webReportId
    ) {
      return true;
    }
    if (
      data.userId.trim().length > 0 &&
      data.pathTx.trim().length > 0 &&
      data.clientId.trim().length === 0 &&
      data.webReportId &&
      data.administrator
    ) {
      return true;
    }
    return false;
  };

  // With local data, just simulate success and update local rows
  const handleUpdate = async () => {
    setSuccessMessage('');
    setValidationError('');
    try {
      // Update or upsert locally
      const targetKey = (r) =>
        r.userId === ref.sUserId &&
        r.clientId === ref.sClientId &&
        String(r.webReportId) === String(ref.iWebReportid);

      const webReportIdNum = Number(data.webReportId) || 0;

      setRowData((prev) => {
        const idx = prev.findIndex(targetKey);
        const updated = {
          userId: data.userId.trimEnd(),
          clientId: data.clientId.trim(),
          pathTx: data.pathTx.trim(),
          webReportId: webReportIdNum,
          webReportIdStr: String(webReportIdNum).padStart(5, '0'),
        };
        if (idx >= 0) {
          const copy = prev.slice();
          copy[idx] = updated;
          return copy;
        }
        return [updated, ...prev];
      });

      handleClear();
      setSuccessMessage('Updated successfully!');
    } catch (err) {
      setValidationError('Failed to update (local).');
    }
  };

  const handleDelete = async () => {
    setSuccessMessage('');
    setValidationError('');
    try {
      setRowData((prev) =>
        prev.filter(
          (r) =>
            !(
              r.userId === ref.sUserId &&
              r.clientId === ref.sClientId &&
              String(r.webReportId) === String(ref.iWebReportid)
            )
        )
      );
      setSuccessMessage('Deleted successfully!');
      handleClear();
    } catch (err) {
      setValidationError('Failed to delete (local).');
    }
  };

  const handleClear = () => {
    setData({
      userId: '',
      clientId: '',
      pathTx: '',
      webReportId: '',
      administrator: false,
    });
    setRef(initialRef);
    setSelectedReportName('');
    setSuccessMessage('');
    setValidationError('');
  };

  const columnDefs = [
    { field: 'webReportIdStr', headerName: 'Report ID', minWidth: 120 },
    { field: 'userId', headerName: 'User ID', minWidth: 140 },
    { field: 'clientId', headerName: 'Client ID', minWidth: 120 },
    { field: 'pathTx', headerName: 'x500 ID', minWidth: 160 },
  ];

  const onGridReady = (params) => {
    gridApi.current = params.api;
  };

  return (
    <div>
      <CRow className="mb-3">
        <CCol xs={12}>
          <CCard>
            <CCardBody>
              <div className="ag-grid-container ag-theme-quartz" style={{ height: '600px' }}>
                <AgGridReact
                  columnDefs={columnDefs}
                  defaultColDef={defaultColDef}
                  // ⬇️ Use client-side row model with local data
                  rowData={rowData}
                  rowModelType="clientSide"
                  pagination={true}
                  paginationPageSize={pageSize}
                  paginationPageSizeSelector={[10, 20, 50, 100]}
                  onGridReady={onGridReady}
                  onRowClicked={(e) => handleTableRowClick(e.data)}
                />
              </div>
            </CCardBody>
          </CCard>
        </CCol>
      </CRow>

      <CRow className="align-items-center mb-3">
        <CCol xs={2}>
          <label htmlFor="input1" className="form-label me-2">User Id:</label>
        </CCol>
        <CCol xs={2}>
          <CFormInput
            id="input1"
            type="text"
            maxLength={8}
            value={data.userId}
            onChange={(e) => setData({ ...data, userId: e.target.value })}
          />
        </CCol>
        <CCol xs={2}>
          <label htmlFor="input2" className="form-label me-2">Client Id:</label>
        </CCol>
        <CCol xs={2}>
          <CFormInput
            id="input2"
            type="text"
            maxLength={4}
            value={data.clientId}
            onChange={(e) => setData({ ...data, clientId: e.target.value })}
            disabled={isClientIdDisabled}
          />
        </CCol>
        <CCol xs={2}>
          <label htmlFor="checkbox1" className="form-label me-2">Administrator:</label>
        </CCol>
        <CCol xs={2}>
          <CFormCheck
            id="checkbox1"
            checked={data.administrator}
            onChange={(e) => setData({ ...data, administrator: e.target.checked })}
            disabled={!isAdminUserEnabled}
          />
        </CCol>
      </CRow>

      <CRow className="mb-3">
        <CCol xs={2}>
          <label htmlFor="" className="form-label me-2">x500 Id:</label>
        </CCol>
        <CCol xs={4}>
          <CFormInput
            id="input3"
            type="text"
            value={data.pathTx}
            onChange={(e) => setData({ ...data, pathTx: e.target.value })}
          />
        </CCol>
      </CRow>

      <CRow className="mb-3">
        <CCol xs={2}>
          <label htmlFor="" className="form-label me-2">Report ID:</label>
        </CCol>
        <CCol>
          <CFormSelect
            name="reportIdList"
            value={String(Number(data.webReportId) || '')}
            onChange={(e) => setData({ ...data, webReportId: e.target.value })}
            disabled={isReportIdSelectDisabled}
          >
            {reportIdList.map((option, index) => (
              <option key={index} value={option.webReportId ?? ''}>
                {option.webReportId ? String(option.webReportId).padStart(5, '0') : ''} {'   '}
                {option.reportName || ''}
              </option>
            ))}
          </CFormSelect>
        </CCol>
      </CRow>

      {successMessage && (
        <CRow className="mb-3">
          <CCol xs={4}>
            <label className="form-label me-2 text-success">{successMessage}</label>
          </CCol>
        </CRow>
      )}
      {validationError && (
        <CRow className="mb-3">
          <CCol xs={8}>
            <p className="text-danger">{validationError}</p>
          </CCol>
        </CRow>
      )}

      <CRow>
        <CCol className="d-flex justify-content-end gap-2">
          <CButton color="primary" type="button" onClick={handleUpdate} disabled={!isChanged()}>Ok</CButton>
          <CButton type="button" color="primary" onClick={handleClear}>Cancel</CButton>
          <CButton type="button" color="primary" onClick={handleUpdate} disabled={!isChanged()}>Update</CButton>
          <CButton type="button" color="primary" onClick={handleDelete} disabled={!isDeleteEnabled}>Delete</CButton>
          <CButton type="button" color="primary" onClick={handleClear}>Clear</CButton>
          <CButton type="button" color="primary">Help</CButton>
        </CCol>
      </CRow>
    </div>
  );
};

export default WebClientDirectory;
