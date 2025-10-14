import React, { useEffect, useState } from 'react';
import {
  Box,
  TextField,
  FormControl,
  Select,
  MenuItem,
  Typography,
  Button,
} from '@mui/material';
import { CCard, CCardBody } from '@coreui/react';

const EditClientReport = ({ selectedGroupRow, isEditable }) => {
  const [tableData, setTableData] = useState([]);
  const [page, setPage] = useState(0);
  const PAGE_SIZE = 10;
  const pageCount = Math.ceil(tableData.length / PAGE_SIZE);
  const pageData = tableData.slice(page * PAGE_SIZE, (page + 1) * PAGE_SIZE);

  useEffect(() => {
    if (!Array.isArray(selectedGroupRow?.reportOptions)) {
      setTableData([]);
      return;
    }

    const clientData = selectedGroupRow.reportOptions.map((option) => ({
      reportName: option?.reportDetails?.queryName || '',
      receive: option.receiveFlag ? '1' : '2',
      destination: option.outputTypeCd !== undefined ? String(option.outputTypeCd) : '',
      fileText: option.fileTypeCd !== undefined ? String(option.fileTypeCd) : '',
      email:
        option.emailFlag === 1
          ? '1'
          : option.emailFlag === 2
          ? '2'
          : '0',
      password: option.reportPasswordTx || '',
    }));
    setTableData(clientData);
  }, [selectedGroupRow]);

  const rowCellStyle = {
    backgroundColor: 'white',
    fontSize: '0.78rem',
    padding: '4px 8px',
    borderBottom: '1px dotted #ddd',
    display: 'flex',
    alignItems: 'center',
    whiteSpace: 'nowrap',
    overflow: 'hidden',
    textOverflow: 'ellipsis',
  };

  const cellSelectSx = {
    fontSize: '0.78rem',
    backgroundColor: 'white',
    '& .MuiSelect-select': {
      padding: '4px 8px',
      fontSize: '0.78rem',
    },
  };

  return (
    <div style={{ padding: '10px' }}>
      <CCard>
        <CCardBody>
          <div style={{ height: '320px', marginBottom: '4px', marginTop: '5px' }}>
            <div style={{ display: 'grid', gridTemplateColumns: '4fr 1fr 1fr 1fr 1fr 1fr', columnGap: '4px' }}>
              {/* Header Row */}
              {['Report Name', 'Receive', 'Destination', 'File Type', 'Email', 'Password'].map((header) => (
                <div
                  key={header}
                  style={{
                    backgroundColor: '#f0f0f0',
                    fontWeight: 'bold',
                    fontSize: '0.78rem',
                    padding: '4px 8px',
                    borderBottom: '1px dotted #ccc',
                  }}
                >
                  {header}
                </div>
              ))}

              {/* Data Rows */}
              {pageData.map((item, index) => (
                <React.Fragment key={index}>
                  {/* Report Name */}
                  <div style={rowCellStyle}>{item.reportName}</div>

                  {/* Receive */}
                  <Select
                    value={item.receive}
                    onChange={(e) => {
                      const updated = [...tableData];
                      updated[index].receive = e.target.value;
                      setTableData(updated);
                    }}
                    size="small"
                    sx={cellSelectSx}
                    disabled={!isEditable}
                  >
                    <MenuItem value="0" sx={{ fontSize: '0.78rem' }}>None</MenuItem>
                    <MenuItem value="1" sx={{ fontSize: '0.78rem' }}>Yes</MenuItem>
                    <MenuItem value="2" sx={{ fontSize: '0.78rem' }}>No</MenuItem>
                  </Select>

                  {/* Destination */}
                  <Select
                    value={item.destination}
                    onChange={(e) => {
                      const updated = [...tableData];
                      updated[index].destination = e.target.value;
                      setTableData(updated);
                    }}
                    size="small"
                    sx={cellSelectSx}
                    disabled={!isEditable}
                  >
                    <MenuItem value="0" sx={{ fontSize: '0.78rem' }}>None</MenuItem>
                    <MenuItem value="1" sx={{ fontSize: '0.78rem' }}>File</MenuItem>
                    <MenuItem value="2" sx={{ fontSize: '0.78rem' }}>Print</MenuItem>
                  </Select>

                  {/* File Type */}
                  <Select
                    value={item.fileText}
                    onChange={(e) => {
                      const updated = [...tableData];
                      updated[index].fileText = e.target.value;
                      setTableData(updated);
                    }}
                    size="small"
                    sx={cellSelectSx}
                    disabled={!isEditable}
                  >
                    <MenuItem value="0" sx={{ fontSize: '0.78rem' }}>None</MenuItem>
                    <MenuItem value="1" sx={{ fontSize: '0.78rem' }}>Text</MenuItem>
                    <MenuItem value="2" sx={{ fontSize: '0.78rem' }}>Excel</MenuItem>
                  </Select>

                  {/* Email */}
                  <Select
                    value={item.email}
                    onChange={(e) => {
                      const updated = [...tableData];
                      updated[index].email = e.target.value;
                      setTableData(updated);
                    }}
                    size="small"
                    sx={cellSelectSx}
                    disabled={!isEditable}
                  >
                    <MenuItem value="0" sx={{ fontSize: '0.78rem' }}>None</MenuItem>
                    <MenuItem value="1" sx={{ fontSize: '0.78rem' }}>Email</MenuItem>
                    <MenuItem value="2" sx={{ fontSize: '0.78rem' }}>Web</MenuItem>
                  </Select>

                  {/* Password */}
                  <TextField
                    type="password"
                    value={item.password}
                    onChange={(e) => {
                      const updated = [...tableData];
                      updated[index].password = e.target.value;
                      setTableData(updated);
                    }}
                    size="small"
                    fullWidth
                    disabled={!isEditable}
                    sx={{
                      '& .MuiInputBase-input': {
                        fontSize: '0.78rem',
                        padding: '4px 8px',
                      },
                    }}
                  />
                </React.Fragment>
              ))}
            </div>
          </div>
          <div style={{ marginTop: '12px', display: 'flex', justifyContent: 'space-between', alignItems: 'center' }}>
            <Button
              variant="text"
              size="small"
              sx={{ fontSize: '0.7rem', padding: '2px 8px', minWidth: 'unset', textTransform: 'none' }}
              onClick={() => setPage((p) => Math.max(p - 1, 0))}
              disabled={page === 0}
            >
              ◀ Previous
            </Button>
            <Typography fontSize="0.75rem">
              Page {tableData.length > 0 ? page + 1 : 0} of {tableData.length > 0 ? pageCount : 0}
            </Typography>
            <Button
              variant="text"
              size="small"
              sx={{ fontSize: '0.7rem', padding: '2px 8px', minWidth: 'unset', textTransform: 'none' }}
              onClick={() => setPage((p) => Math.min(p + 1, pageCount - 1))}
              disabled={page === pageCount - 1}
            >
              Next ▶
            </Button>
          </div>
        </CCardBody>
      </CCard>
    </div>
  );
};

export default EditClientReport;
