import { Button, Typography } from '@mui/material';
import React, { useState, useEffect } from 'react';
import { CRow, CCol, CCard, CCardBody } from '@coreui/react';

const PAGE_SIZE = 10;
const COLUMNS = 4;

const PreviewFilesSentTo = ({ data }) => {
  const [page, setPage] = useState(0);

  const pageCount = Math.ceil((data?.length || 0) / PAGE_SIZE);
  const pageData = data?.slice(page * PAGE_SIZE, (page + 1) * PAGE_SIZE) || [];

  useEffect(() => {
    if (data && data.length > 0) {
      console.info(JSON.stringify(data, null, 2));
    }
  }, [data]);

  const hasData = data && data.length > 0;

  const cellStyle = {
    backgroundColor: 'white',
    minHeight: '25px',
    display: 'flex',
    alignItems: 'center',
    justifyContent: 'flex-start',
    fontSize: '0.78rem',
    fontWeight: 200,
    padding: '0 10px',
    borderBottom: '1px dotted #ddd',
  };

  const headerStyle = {
    ...cellStyle,
    fontWeight: 'bold',
    backgroundColor: '#f0f0f0',
    borderBottom: '1px dotted #ccc',
  };

  return (
    <>
      <CCard
        style={{
          height: '35px',
          marginBottom: '4px',
          marginTop: '2px',
          border: 'none',
          backgroundColor: '#f3f6f8',
          boxShadow: 'none',
          borderRadius: '4px',
        }}
      >
        <CCardBody
          className="d-flex align-items-center"
          style={{ padding: '0.25rem 0.5rem', height: '100%', backgroundColor: 'transparent' }}
        >
          <p style={{ margin: 0, fontSize: '0.78rem', fontWeight: '500' }}>General</p>
        </CCardBody>
      </CCard>

      <CCard style={{ height: '320px', marginBottom: '4px', marginTop: '15px' }}>
        <CCardBody className="d-flex align-items-center" style={{ padding: '0.25rem 0.5rem', height: '100%' }}>
          <div style={{ width: '100%', height: '100%' }}>
            <div style={{ display: 'flex', flexDirection: 'column', height: '100%' }}>
              {/* Grid Table */}
              <div
                style={{
                  flex: 1,
                  display: 'grid',
                  gridTemplateColumns: '1fr',
                  rowGap: '0px',
                  columnGap: '4px',
                  minHeight: '250px',
                  alignContent: 'start',
                }}
              >
                {/* Header Row */}
                <div style={headerStyle}>Name</div>

                {/* Data Rows */}
                {pageData.length > 0 ? (
                  pageData.map((item, index) => (
                    <React.Fragment key={`${item.vendorId}-${index}`}>
                      <div style={cellStyle}>{item.vendorName?.trim() || ''}</div>
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
          </div>
        </CCardBody>
      </CCard>
    </>
  );
};

export default PreviewFilesSentTo;
