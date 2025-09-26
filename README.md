import { useEffect, useRef, useState, useMemo } from 'react';
import {
    CButton, CFormInput, CCol, CFormCheck, CRow, CCard, CFormSelect,
    CCardBody
} from '@coreui/react';
import './WebClientDirectory.scss';

import { AllCommunityModule, ModuleRegistry } from 'ag-grid-community';
import { AgGridReact } from 'ag-grid-react';

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

    useEffect(() => {
        fetchReportIdList();
    }, []);

    const fetchReportIdList = async () => {
        try {
            const response = await fetch('http://localhost:8081/api/web-client-directory/report-id');
            if (!response.ok) throw new Error('Failed to fetch data');
            const data = await response.json();
            setReportIdList([{}, ...data]);
        } catch (err) {
            setValidationError(err.message);
        }
    };

    const dataSource = useMemo(() => ({
        getRows: async (params) => {
            const page = params.startRow / pageSize;
            try {
                const url = `http://localhost:8081/api/web-client-directory/all?page=${page}&size=${pageSize}&sortBy=id&sortDir=asc`;
                const response = await fetch(url);
                const data = await response.json();
                const rowsThisPage = data.content.map(element => ({
                    webReportId: element.id.webReportId ? element.id.webReportId.toString().padStart(5, '0') : '',
                    userId: element.id.userId,
                    clientId: element.clientId,
                    pathTx: element.pathTx,
                }));
                params.successCallback(rowsThisPage, data.totalElements);
            } catch (err) {
                params.failCallback();
            }
        }
    }), [pageSize]);

    const handleTableRowClick = (item) => {
        setTimeout(() => {
            setData({
                userId: item.userId,
                clientId: item.clientId,
                pathTx: item.pathTx,
                webReportId: item.webReportId,
                administrator: item.clientId === '' ? true : false,
            });
            setRef({
                sUserId: item.userId,
                sClientId: item.clientId,
                sWebDirectoryPath: item.pathTx,
                iWebReportid: item.webReportId,
            });
            setSelectedReportName(
                reportIdList.find(r => r.webReportId === item.webReportId)?.reportName || ''
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
            setData(prev => ({ ...prev, administrator: false }));
        }
    }, [data.clientId]);

    const isChanged = () => {
        if (
            data.userId.trim() === ref.sUserId.trim() &&
            data.clientId.trim() === ref.sClientId.trim() &&
            data.pathTx.trim() === ref.sWebDirectoryPath.trim() &&
            data.webReportId === ref.iWebReportid
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

    const handleUpdate = async () => {
        setSuccessMessage('');
        setValidationError('');
        try {
            let url = 'http://localhost:8081/api/web-client-directory/update';
            let method = 'POST';
            let body = {
                userId: data.userId.trim(),
                clientId: data.clientId.trim(),
                pathTx: data.pathTx.trim(),
                webReportId: String(Number(data.webReportId)),
            };
            const response = await fetch(url, {
                method,
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify(body),
            });
            if (!response.ok) throw new Error('Failed to update');
            handleClear();
            dataSource();
            setSuccessMessage('Updated successfully!');
            if (gridApi.current) {
                gridApi.current.refreshInfiniteCache();
            }
        } catch (err) {
            setValidationError(err.message);
        }
    };

    const handleDelete = async () => {
        setSuccessMessage('');
        setValidationError('');
        try {
            let url = 'http://localhost:8081/api/web-client-directory/delete';
            let method = 'POST';
            let body = {
                userId: ref.sUserId,
                clientId: ref.sClientId,
                pathTx: ref.sWebDirectoryPath,
                webReportId: String(Number(ref.iWebReportid)),
            };
            const response = await fetch(url, {
                method,
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify(body),
            });
            if (!response.ok) throw new Error('Failed to delete');
            setSuccessMessage('Deleted successfully!');
            if (gridApi.current) {
                gridApi.current.refreshInfiniteCache();
            }
            handleClear();
        } catch (err) {
            setValidationError(err.message);
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
        { field: 'webReportId', headerName: 'Report ID' },
        { field: 'userId', headerName: 'User ID' },
        { field: 'clientId', headerName: 'Client ID' },
        { field: 'pathTx', headerName: 'x500 ID' },
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
                                    rowModelType="infinite"
                                    pagination={true}
                                    paginationPageSize={pageSize}
                                    paginationPageSizeSelector={[10, 20, 50, 100]}
                                    cacheBlockSize={pageSize}
                                    maxBlocksInCache={2}
                                    onGridReady={onGridReady}
                                    datasource={dataSource}
                                    onRowClicked={e => handleTableRowClick(e.data)}
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
                        onChange={e => setData({ ...data, userId: e.target.value })}
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
                        onChange={e => setData({ ...data, clientId: e.target.value })}
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
                        onChange={e => setData({ ...data, administrator: e.target.checked })}
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
                        id="input2"
                        type="text"
                        maxLength={4}
                        value={data.pathTx}
                        onChange={e => setData({ ...data, pathTx: e.target.value })}
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
                        value={String(Number(data.webReportId))}
                        onChange={e => setData({ ...data, webReportId: e.target.value })}
                        disabled={isReportIdSelectDisabled}
                    >
                        {reportIdList.map((option, index) => (
                            <option key={index} value={option.webReportId}>
                                {option.webReportId ? option.webReportId.toString().padStart(5, '0') : ''} {'   '} {option.reportName || ''}
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




// src/views/.../WebClientDirectory/data.js
export const webClientDirectoryRows = [
  {
    "userId": "00000   ",
    "webReportId": 1,
    "clientId": "    ",
    "pathTx": "EGGGG"
  },
  {
    "userId": "00000001",
    "webReportId": 1,
    "clientId": "0111",
    "pathTx": "TESTING"
  },
  {
    "userId": "0085boyl",
    "webReportId": 1,
    "clientId": "22ee",
    "pathTx": "WWWE"
  },
  {
    "userId": "0085boyl",
    "webReportId": 6,
    "clientId": "22ee",
    "pathTx": "WWWE"
  },
  {
    "userId": "0085boyl",
    "webReportId": 10,
    "clientId": "22ee",
    "pathTx": "WWWE"
  },
  {
    "userId": "01      ",
    "webReportId": 5,
    "clientId": "    ",
    "pathTx": "11"
  },
  {
    "userId": "01313128",
    "webReportId": 30,
    "clientId": "edft",
    "pathTx": "AAAA1114"
  },
  {
    "userId": "01440   ",
    "webReportId": 14,
    "clientId": "5945",
    "pathTx": "AAAA05231"
  },
  {
    "userId": "01440   ",
    "webReportId": 15,
    "clientId": "5945",
    "pathTx": "AAAA05231"
  },
  {
    "userId": "0193    ",
    "webReportId": 6,
    "clientId": "    ",
    "pathTx": "GHH"
  }
];

