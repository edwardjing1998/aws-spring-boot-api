import React, { useEffect, useMemo, useState } from 'react';
import { ModuleRegistry, AllCommunityModule } from 'ag-grid-community';
import { AgGridReact } from 'ag-grid-react';
import 'ag-grid-community/styles/ag-grid.css';
import 'ag-grid-community/styles/ag-theme-quartz.css';
import { clientReportMappingData } from './data';

ModuleRegistry.registerModules([AllCommunityModule]);

export default function TestGrid() {
  const [rowData, setRowData] = useState([]);
  const columnDefs = useMemo(() => ([
    { field: 'clientId', headerName: 'Client', maxWidth: 120 },
    { field: 'reportClientId', headerName: 'Report Client', maxWidth: 160 },
    { field: 'sendToBothInd', headerName: 'Send To Both', maxWidth: 140,
      valueGetter: p => (p.data?.sendToBothInd ? 'Yes' : 'No') },
    { field: 'description', headerName: 'Description', minWidth: 200, flex: 2 },
  ]), []);

  useEffect(() => {
    const rows = clientReportMappingData?.response?.clientReportMapping ?? [];
    console.log('rows length:', rows.length);
    setRowData(rows.map((r, i) => ({ id: i + 1, ...r })));
  }, []);

  return (
    <div className="ag-theme-quartz" style={{ height: 500, width: '100%' }}>
      <AgGridReact
        rowData={rowData}
        columnDefs={columnDefs}
        defaultColDef={{ flex: 1, minWidth: 100, sortable: true, filter: true }}
        pagination={true}
        paginationPageSize={10}
      />
    </div>
  );
}
