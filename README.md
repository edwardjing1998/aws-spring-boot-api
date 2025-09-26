import React, { useState, useEffect, useMemo, useRef } from 'react';
import { ModuleRegistry, AllCommunityModule } from 'ag-grid-community';
import { AgGridReact } from 'ag-grid-react';
import { Button } from '@mui/material';
import { CFormTextarea, CFormInput, CFormCheck, CFormSelect } from '@coreui/react';
import * as clientReportSvc from '../../../services/AdminMaintenanceService/ClientReportMappingService';
import CustomSnackbar from '../../../components/CustomSnackbar';
import { EditIcon, ClientMappingDeleteIcon, SearchIcon } from '../../../assets/brand/svg-constants';
import '../../../scss/ClientReportMapping.scss';

ModuleRegistry.registerModules([AllCommunityModule]);

const ClientReportMapping = () => {
  const containerStyle = useMemo(() => ({ width: '100%', height: '89%' }), []);
  const gridStyle = useMemo(() => ({ height: '100%', width: '100%' }), []);
  const defaultColDef = useMemo(() => ({ flex: 1, minWidth: 100 }), []);
  const rowSelection = useMemo(() => ({ mode: 'multiRow' }), []);

  const [columnDefs] = useState([
    { field: 'aspId', headerName: '#', minWidth: 75 },
    { field: 'activeInactive', headerName: 'Indicator', minWidth: 97 },
    { field: 'name', headerName: 'Name' },
    { field: 'emailAddress', headerName: 'Email' },
    { field: 'application', headerName: 'Application' },
    { field: 'startHour', headerName: 'Start Hr', minWidth: 50 },
    { field: 'endHour', headerName: 'End Hr', minWidth: 50 },
    { field: 'days', headerName: 'Week Days', cellRenderer: 'WeekDaysRenderer', cellClass: 'days-cell', minWidth: 150 },
    {
      headerName: '',
      minWidth: 127,
      field: 'actions',
      cellClass: 'actions-cell-flex-end',
      cellRenderer: (params) => (
        <div className="actions-cell icon-container action-cell-flex">
          <span className="icon-wrapper">
            <EditIcon style={{ cursor: 'pointer' }} onClick={() => params.context.handleEditClick(params.data)} />
          </span>
          <span className="icon-wrapper">
            <ClientMappingDeleteIcon
              data-testid="delete-icon"
              style={{ cursor: 'pointer' }}
              onClick={() => params.context.handleDeleteClick(params.da
