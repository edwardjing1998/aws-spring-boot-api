import { Button, Typography } from '@mui/material';
import React, { useState, useEffect, useMemo } from 'react';
import { CRow, CCol, CCard, CCardBody } from '@coreui/react';

const PAGE_SIZE = 10;

const PreviewFilesSentTo = ({ data }) => {
  const [page, setPage] = useState(0);

  // Normalize incoming rows to a consistent shape
  const rows = useMemo(() => {
    const list = Array.isArray(data) ? data : [];
    return list.map((r, idx) => {
      const id =
        r?.vendId ??
        r?.vendorId ??
        r?.vendor?.vendId ??
        r?.vendor?.id ??
        String(idx);
      const name =
        r?.vendName ??
        r?.vendorName ??
        r?.vendor?.vendNm ??
        r?.vendor?.name ??
        String(id);
      const qRaw =
        r?.queueForMail ??
        r?.queForMail ??
        r?.que_for_mail ??
        r?.queForMailCd ??
        r?.queue_for_mail_cd;
      // normalize boolean-ish values
      let q = false;
      if (typeof qRaw === 'string') {
        const s = qRaw.trim().toUpperCase();
        q = s === '1' || s === 'Y' || s === 'TRUE';
      } else if (typeof qRaw === 'number') {
        q = qRaw === 1;
      } else {
        q = Boolean(qRaw);
      }
      return { id: String(id), name: String(name ?? ''), queueForMail: q };
    });
  }, [data]);

  const pageCount = Math.ceil(rows.length / PAGE_SIZE) || 0;

  // Clamp / reset page when data length changes
  useEffect(() => {
    if (page > 0 && page >= pageCount) {
      setPage(Math.max(0, pageCount - 1));
    }
  }, [page, pageCount]);

  // Optional: reset to first page when the dataset identity changes
  useEffect(() => {
    setPage(0);
  }, [rows.length]);

  const pageData =
    rows.length > 0
      ? rows.slice(page * PAGE_SIZE, (page + 1) * PAGE_SIZE)
      : [];

  const hasData = rows.length > 0;

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
                  pageData.map((item) => (
                    <React.Fragment key={item.id}>
                      <div style={cellStyle}>
                        {item.name?.trim() || item.id}
                        {item.queueForMail ? ' (Q)' : ''}
                      </div>
                    </React.Fragment>
                  ))
                ) : (
                  <Typography sx={{ fontSize: '0.75rem', padding: '0 16px' }}>
                    No vendors to show.
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

export default PreviewFilesSentTo;
