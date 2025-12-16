import { Button, Typography } from '@mui/material';
import React, { useState, useEffect } from 'react';

const PAGE_SIZE = 12; // 4x4 grid
const COLUMNS = 4;

const PreviewClientSysPrinList = ({ data, sysPrinTotal }) => {
  const [page, setPage] = useState(0);

  const pageCount = Math.ceil((data?.length || 0) / PAGE_SIZE);
  const pageData = data?.slice(page * PAGE_SIZE, (page + 1) * PAGE_SIZE) || [];
  const hasData = data && data.length > 0;

 // alert("sysPrinTotal " + sysPrinTotal);

  const cellStyle = {
    backgroundColor: 'white',
    minHeight: '25px',
    display: 'flex',
    alignItems: 'center',
    justifyContent: 'flex-start',
    fontSize: '0.78rem',
    fontWeight: 150,
    padding: '0 10px',
    borderRadius: '0px',
    borderBottom: '1px dotted #ddd'
  };

  const headerStyle = {
    ...cellStyle,
    fontWeight: 'bold',
    backgroundColor: '#f0f0f0'
  };

  return (
    <div style={{ display: 'flex', flexDirection: 'column', height: '100%' }}>
      {/* Grid Table */}
      <div
        style={{
          flex: 1,
          display: 'grid',
          gridTemplateColumns: '1fr 1fr 1fr 1fr',
          rowGap: '0px',
          columnGap: '0px',
          minHeight: '200px',
          alignContent: 'start'
        }}
      >
        {/* Header Row */}
        <div style={headerStyle}>SysPrin</div>
        <div style={headerStyle}>Active</div>
        <div style={headerStyle}>SysPrin</div>
        <div style={headerStyle}>Active</div>

        {/* Data Rows */}
        {pageData.length > 0 ? (
          pageData.reduce((rows, item, index) => {
            if (index % 2 === 0) {
              const second = pageData[index + 1];
              rows.push(
                <React.Fragment key={index}>
                  <div style={cellStyle}>{item.sysPrin}</div>
                  <div style={cellStyle}>{item.sysPrinActive ? 'Yes' : 'No'}</div>
                  <div style={cellStyle}>{second?.sysPrin || ''}</div>
                  <div style={cellStyle}>{second ? (second.sysPrinActive ? 'Yes' : 'No') : ''}</div>
                </React.Fragment>
              );
            }
            return rows;
          }, [])
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
          alignItems: 'center'
        }}
      >
        <Button
          variant="text"
          size="small"
          sx={{ fontSize: '0.7rem', padding: '2px 8px', minWidth: 'unset', textTransform: 'none' }}
          onClick={() => setPage((p) => Math.max(p - 1, 0))}
          disabled={!hasData || page === 0}
        >
          ◀ Previous
        </Button>

        <Typography fontSize="0.75rem">
          Page {hasData ? page + 1 : 0} of {hasData ? pageCount : 0}
        </Typography>

        <Button
          variant="text"
          size="small"
          sx={{ fontSize: '0.7rem', padding: '2px 8px', minWidth: 'unset', textTransform: 'none' }}
          onClick={() => setPage((p) => Math.min(p + 1, pageCount - 1))}
          disabled={!hasData || page === pageCount - 1}
        >
          Next ▶
        </Button>
      </div>
    </div>
  );
};

export default PreviewClientSysPrinList;
