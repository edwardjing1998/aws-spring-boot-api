import { Button, Typography } from '@mui/material';
import React, { useState, useEffect } from 'react';

const PAGE_SIZE = 8; // Match your API call size
const COLUMNS = 2;

const PreviewClientReports = ({ data, reportOptionTotal }) => {
  const [page, setPage] = useState(0);
  const [reports, setReports] = useState(data || []);
  
  // Try to find a clientId from the initial data passed in
  const clientId = data && data.length > 0 ? data[0].clientId : null;

  // We need total elements to know when to disable "Next". 
  // We use the reportOptionTotal prop passed from parent if available.
  const totalCount = reportOptionTotal; 

  useEffect(() => {
    // If we are on page 0 and have initial data, we might not need to fetch immediately,
    // but consistent behavior suggests fetching or using what we have.
    // If 'data' prop changes (e.g. parent selection changes), reset to page 0
    if (data) {
        setReports(data);
        setPage(0);
    }
  }, [data]);

  // Fetch data when page changes or clientId changes
  useEffect(() => {
    // If we don't have a client ID, we can't fetch.
    if (!clientId) return;

    // Optimization: If page is 0 and we just received 'data' from props, 
    // we might already have the data. However, to support pagination
    // we need to fetch subsequent pages. 
    // For simplicity, let's fetch if page > 0 OR if we want to ensure fresh data.
    
    // Actually, the initial 'data' prop usually contains just the first page (or all, depending on parent implementation).
    // If the parent provided the first page, we use it.
    if (page === 0 && data && data.length > 0 && data[0].clientId === clientId) {
        setReports(data);
        return; 
    }

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
  }, [page, clientId, data]); 
  // Note: Including 'data' in dependency array might cause re-fetch on initial load 
  // if not handled carefully, but the check `if (page === 0 && data ...)` prevents redundant call.

  const hasData = reports && reports.length > 0;
  
  // Calculate total pages if totalCount is provided
  const pageCount = totalCount ? Math.ceil(totalCount / PAGE_SIZE) : 0;

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
          Page {page + 1} {totalCount !== undefined ? `of ${pageCount}` : ''}
        </Typography>

        <Button
          variant="text"
          size="small"
          sx={{ fontSize: '0.7rem', padding: '2px 8px', minWidth: 'unset', textTransform: 'none' }}
          onClick={() => setPage((p) => p + 1)}
          // Disable next if we are on the last page (0-indexed comparison)
          // If totalCount is unknown, we rely on whether the current fetch returned a full page
          disabled={totalCount !== undefined ? (page >= pageCount - 1) : (reports.length < PAGE_SIZE)}
        >
          Next ▶
        </Button>
      </div>
    </div>
  );
};

export default PreviewClientReports;
