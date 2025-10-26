import { Button, Typography } from '@mui/material';
import React, { useState, useEffect, useMemo } from 'react';
import { CCard, CCardBody } from '@coreui/react';

const PAGE_SIZE = 10;
const COLUMNS = 4;

const PreviewFilesReceivedFrom = ({ data }) => {
  const [page, setPage] = useState(0);

  // Normalize rows: compute a consistent id + display name
  const rows = useMemo(() => {
    const arr = Array.isArray(data) ? data : [];

    return arr.map((it) => {
      // id candidates
      const id =
        it.vendorId ??
        it.vendId ??
        it.id ??
        it.vendor?.vendId ??
        it.vendor?.id ??
        '';

      // name candidates (include vendName + vendNm)
      const name =
        it.vendName ??
        it.vendorName ??
        it.vendor?.vendNm ??
        it.vendor?.name ??
        it.name ??
        it.vend_nm ??
        '';

      const displayName = (name || '').toString().trim();
      const displayId = (id || '').toString().trim();

      // flag candidates
      const queueRaw =
        it.queueForMail ??
        it.queForMail ??
        it.que_for_mail ??
        it.queForMailCd ??
        it.queue_for_mail_cd;
      const queueForMail =
        typeof queueRaw === 'string'
          ? ['1', 'Y', 'TRUE'].includes(queueRaw.trim().toUpperCase())
          : !!queueRaw;

      return {
        ...it,
        __id: displayId,
        __displayName: displayName || displayId, // fallback to ID if no name
        __q: queueForMail,
      };
    });
  }, [data]);

  const pageCount = Math.ceil(rows.length / PAGE_SIZE) || 0;
  const pageData = rows.slice(page * PAGE_SIZE, (page + 1) * PAGE_SIZE);
  const hasData = rows.length > 0;

  // Reset page if data shrinks
  useEffect(() => {
    if (page > 0 && page >= pageCount) setPage(0);
  }, [page, pageCount]);

  // Helpful log (optional)
  useEffect(() => {
    if (hasData) console.info('[PreviewFilesReceivedFrom] rows:', rows);
  }, [rows, hasData]);

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
          <p style={{ margin: 0, fontSize: '0.78rem', fontWeight: 500 }}>General</p>
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
                    <div
                      key={`${item.__id || index}-${index}`}
                      style={cellStyle}
                      title={item.__id || ''}
                    >
                      {item.__displayName || <em style={{ color: '#6b7280' }}>(no name)</em>}
                      {item.__q ? ' (Q)' : ''}
                    </div>
                  ))
                ) : (
                  <Typography
                    sx={{
                      gridColumn: `span ${COLUMNS}`,
                      fontSize: '0.75rem',
                      padding: '0 16px',
                      color: 'text.secondary',
                    }}
                  >
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
                  onClick={() => setPage((p) => Math.min(p + 1, Math.max(pageCount - 1, 0)))}
                  disabled={!hasData || page >= pageCount - 1}
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

export default PreviewFilesReceivedFrom;
