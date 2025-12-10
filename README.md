import { Button, Typography } from '@mui/material';
import React, { useState, useEffect } from 'react';

const PAGE_SIZE = 8; // Match your API call size
const COLUMNS = 2;

const PreviewClientReports = ({ clientId, initialData }) => {
  const [page, setPage] = useState(0);
  const [reports, setReports] = useState(initialData || []);
  // We need total elements to know when to disable "Next". 
  // Ideally your API returns this in a wrapper (like Page<T>), 
  // but your sample returns a raw List []. 
  // So we might just check if the returned list size < PAGE_SIZE to know if it's the last page.
  const [hasMore, setHasMore] = useState(true); 

  useEffect(() => {
    // If initialData is provided for page 0, use it.
    if (page === 0 && initialData) {
      setReports(initialData);
      setHasMore(initialData.length === PAGE_SIZE);
      return;
    }

    if (!clientId) return;

    // Fetch data from API
    const fetchData = async () => {
      try {
        const url = `http://localhost:8089/client-sysprin-reader/api/client/report-option/${encodeURIComponent(clientId)}?page=${page}&size=${PAGE_SIZE}`;
        
        const resp = await fetch(url, {
            method: 'GET',
            headers: { accept: '*/*' },
        });

        if (resp.ok) {
            const json = await resp.json();
            setReports(json);
            // If we got fewer items than requested, we reached the end
            setHasMore(json.length === PAGE_SIZE);
        } else {
            console.error("Failed to fetch reports");
            setReports([]);
        }
      } catch (error) {
        console.error("Error fetching reports:", error);
        setReports([]);
      }
    };

    fetchData();
  }, [page, clientId, initialData]);

  const hasData = reports && reports.length > 0;

  const cellStyle = {
    backgroundColor: 'white',
    minHeight: '25px',
    display: 'flex',
    alignItems: 'center',
    justifyContent: 'flex-start',
    fontSize: '0.78rem',
    fontWeight: 200,
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
          gridTemplateColumns: '410px 120px 120px 120px',
          rowGap: '0px',
          columnGap: '4px',
          minHeight: '100px',
          alignContent: 'start'
        }}
      >
        {/* Header Row */}
        <div style={headerStyle}>Name</div>
        <div style={headerStyle}>Received</div>
        <div style={headerStyle}>Type</div>
        <div style={headerStyle}>Output</div>

        {/* Data Rows - directly map 'reports' which is now the current page */}
        {hasData ? (
          reports.map((item, index) => (
            <React.Fragment key={`${item.reportId}-${index}`}>
              <div style={cellStyle}>{item.reportDetails?.queryName?.trim() || ''}</div>
              <div style={cellStyle}>{item.receiveFlag ? 'Yes' : 'No'}</div>
              <div style={cellStyle}>{item.reportDetails?.fileExt || ''}</div>
              <div style={cellStyle}>{item.outputTypeCd}</div>
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
          alignItems: 'center'
        }}
      >
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
          Page {page + 1} 
          {/* We don't know total pages without a 'total' field in API response */}
        </Typography>

        <Button
          variant="text"
          size="small"
          sx={{ fontSize: '0.7rem', padding: '2px 8px', minWidth: 'unset', textTransform: 'none' }}
          onClick={() => setPage((p) => p + 1)}
          disabled={!hasData || !hasMore}
        >
          Next ▶
        </Button>
      </div>
    </div>
  );
};

export default PreviewClientReports;
