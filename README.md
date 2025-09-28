import React, { useRef, useState, useMemo } from "react";
import { CFormInput } from '@coreui/react';
import * as InputRobotTotalsService from '../../../../services/AdminReportService/InputRobotTotalsService';
import { RobotLabelProductivity } from '../../../../assets/brand/svg-constants';
import { AllCommunityModule, ModuleRegistry, InfiniteRowModelModule } from 'ag-grid-community';
import { AgGridReact } from 'ag-grid-react';
import '../../../../scss/InputRobotTotals.scss';
import CustomSnackbar from '../../../../components/CustomSnackbar';

// ✅ ag-Grid v33+: register InfiniteRowModelModule explicitly
ModuleRegistry.registerModules([AllCommunityModule, InfiniteRowModelModule]);

const defaultColDef = { flex: 1 };

const InputRobotTotals = () => {
  // Layout sizes
  const containerStyle = useMemo(() => ({ width: "100%", height: 520 }), []);
  const gridStyle = useMemo(() => ({ height: "100%", width: "100%" }), []);

  // Grid settings
  const rowSelection = useMemo(() => ({ mode: 'multiRow' }), []);
  const [pageSize, setPageSize] = useState(10);
  const gridApi = useRef(null);

  // Toolbar state
  const [selectedDate, setSelectedDate] = useState(() => {
    const today = new Date();
    return today.toISOString().slice(0, 10);
  });

  // Edit tracking
  const [editedRows, setEditedRows] = useState({});
  const [snackbarType, setSnackbarType] = useState('none');

  // AG Grid datasource (Infinite Row Model)
  const createDataSource = (pageSizeArg) => ({
    getRows: (params) => {
      const page = params.startRow / pageSizeArg;
      InputRobotTotalsService.fetchInputRobotTotals(page, pageSizeArg)
        .then((data) => {
          const response = data.response.RobotLabelList;
          let rows, lastRow;
          if (response && Array.isArray(response.rows) && typeof response.totalElements === 'number') {
            rows = response.rows;
            lastRow = response.totalElements;
          } else if (Array.isArray(response) && typeof response.totalElements === 'number') {
            rows = response;
            lastRow = response.totalElements;
          } else if (Array.isArray(response)) {
            rows = response;
            lastRow = rows.length < pageSizeArg ? params.startRow + rows.length : -1;
          } else {
            rows = [];
            lastRow = 0;
          }

          // add S.NO based on the current block
          rows = rows.map((row, idx) => ({
            ...row,
            sNo: params.startRow + idx + 1,
          }));

          params.successCallback(rows, lastRow);
        })
        .catch(() => {
          params.failCallback();
        });
    },
  });

  const dataSource = useMemo(() => createDataSource(pageSize), [pageSize]);

  // Columns
  const columnDefs = useMemo(
    () => [
      { field: 'sNo', headerName: 'S.NO' },
      { field: 'userName', headerName: 'Operator', editable: true },
      { field: 'bundles', headerName: 'Bundles', editable: true },
      { field: 'individualLabels', headerName: 'Individual Labels', editable: true },
    ],
    [],
  );

  const onGridReady = (params) => {
    gridApi.current = params.api;
  };

  const handleCellValueChanged = (params) => {
    const updatedRow = params.data;
    setEditedRows({ updatedRow });
  };

  // Page-style actions
  const handleClear = () => {
    setEditedRows({});
    // Optionally refresh data
    gridApi.current?.refreshInfiniteCache();
  };

  const handleApply = async () => {
    if (!editedRows?.updatedRow || Object.keys(editedRows.updatedRow).length === 0) {
      return;
    }
    const { sNo, reportingDate, ...rowWithoutSNo } = editedRows.updatedRow || {};
    const payload = {
      ...rowWithoutSNo,
      reportingDate: `${selectedDate}T00:00:00`,
    };
    try {
      await InputRobotTotalsService.updateInputRobotTotal(payload);
      setEditedRows({});
      setSnackbarType('update');
      // Optionally refresh to reflect persisted values
      gridApi.current?.refreshInfiniteCache();
    } catch (err) {
      // You can surface an error snackbar if desired
    }
  };

  const handleSnackbarCancel = () => {
    setEditedRows({});
    setSnackbarType('none');
  };

  return (
    <div className="input-robot-totals-page">
      {/* Header / Title */}
      <div className="irt-header" style={{ display: 'flex', alignItems: 'center', gap: 8, marginBottom: 12 }}>
        <div className="input-robot-totals-icon"><RobotLabelProductivity /></div>
        <h2 style={{ margin: 0 }}>Robot Labels Productivity</h2>
      </div>

      {/* Toolbar */}
      <div className="irt-toolbar" style={{ display: 'flex', alignItems: 'center', gap: 12, marginBottom: 12 }}>
        <div className="date-layout" style={{ display: 'flex', alignItems: 'center', gap: 8 }}>
          <span className="date-text">Date</span>
          <CFormInput
            type="date"
            className="date-input"
            value={selectedDate}
            onChange={(e) => setSelectedDate(e.target.value)}
            style={{ maxWidth: 180 }}
          />
        </div>

        <div style={{ marginLeft: 'auto', display: 'flex', gap: 8 }}>
          <button
            className="btn btn-outline-secondary"
            onClick={handleClear}
            type="button"
          >
            Clear
          </button>
          <button
            className="btn btn-primary"
            onClick={handleApply}
            type="button"
            disabled={!editedRows?.updatedRow || Object.keys(editedRows.updatedRow).length === 0}
          >
            Apply
          </button>
        </div>
      </div>

      {/* Grid */}
      <div className="ag-theme-quartz" style={containerStyle}>
        <div style={gridStyle}>
          <AgGridReact
            columnDefs={columnDefs}
            rowModelType="infinite"
            defaultColDef={defaultColDef}
            rowSelection={rowSelection}
            pagination={true}
            paginationPageSize={pageSize}
            paginationPageSizeSelector={[10, 20, 50, 100]}
            cacheBlockSize={pageSize}
            infiniteInitialRowCount={pageSize}
            cacheOverflowSize={1}
            maxConcurrentDatasourceRequests={1}
            maxBlocksInCache={2}
            onGridReady={onGridReady}
            datasource={dataSource}
            onSelectionChanged={(params) => {
              // if you need selected rows later
              // const sel = params.api.getSelectedRows();
            }}
            onPaginationChanged={(params) => {
              const newPageSize = params.api.paginationGetPageSize();
              setPageSize((prev) => (prev !== newPageSize ? newPageSize : prev));
            }}
            onCellValueChanged={handleCellValueChanged}
          />
        </div>
      </div>

      {/* Snackbar */}
      <CustomSnackbar
        type={snackbarType}
        open={snackbarType !== 'none'}
        handleOk={handleSnackbarCancel}
        onClose={handleSnackbarCancel}
        title={snackbarType === 'update' ? 'Robot Labels Productivity' : ''}
        body={snackbarType === 'update' ? 'You have successfully Updated a client' : ''}
      />
    </div>
  );
};

export default InputRobotTotals;
