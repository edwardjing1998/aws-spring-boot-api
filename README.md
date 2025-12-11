import { Button, Typography } from '@mui/material';
import React, { useState, useEffect } from 'react';

const PAGE_SIZE = 8; // Match your API call size
const COLUMNS = 2;

const PreviewClientReports = ({ data, reportOptionTotal }) => {
  // Store page index per client: { "0042": 1, "0043": 0 }
  const [pageMap, setPageMap] = useState({});
  const [reports, setReports] = useState(data || []);
  
  // Try to find a clientId from the initial data passed in
  const clientId = data && data.length > 0 ? data[0].clientId : null;

  // Get current page for this client, default to 0
  const page = clientId ? (pageMap[clientId] || 0) : 0;

  // We need total elements to know when to disable "Next". 
  // We use the reportOptionTotal prop passed from parent if available.
  const totalCount = reportOptionTotal; 

  // Helper to update page for specific client
  const setClientPage = (newPage) => {
    if (!clientId) return;
    setPageMap(prev => ({
        ...prev,
        [clientId]: newPage
    }));
  };

  // React to data updates from parent (e.g. after a save)
  useEffect(() => {
    if (data) {
        // 1. Update local state with the new data (usually page 0 from parent refresh)
        setReports(data);
        
        // 2. Force reset to Page 0. This ensures we see the new data and start fresh.
        if (clientId) {
            setPageMap(prev => ({
                ...prev,
                [clientId]: 0
            }));
        }
    }
  }, [data, clientId]);

  // Fetch data when page changes or clientId changes
  // Note: We check if page > 0 to avoid double-fetching page 0 if 'data' prop already provided it.
  // However, if you want to FORCE a fetch of page 0 on update, the logic inside useEffect takes care of it
  // because setPageMap(0) triggers this effect.
  useEffect(() => {
    if (!clientId) return;

    // Optimization: If we are on page 0 and 'data' prop was just updated with records, 
    // we can skip the fetch if 'data' is already what we want.
    // BUT, to ensure we get the absolute latest state after an add, we can let it fetch.
    // Given your request to "call the rest api... page=0", we allow the fetch.
    
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
  }, [page, clientId]); // Removed 'data' from dependency to avoid loop if setReports updates it

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
          // Use setClientPage to update the map
          onClick={() => setClientPage(Math.max(page - 1, 0))}
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
          // Use setClientPage to update the map
          onClick={() => setClientPage(page + 1)}
          // Disable next if we are on the last page (0-indexed comparison)
          // Fix: Allow moving next if current page is full (length == PAGE_SIZE), 
          // even if totalCount says we are done (handles stale totalCount from parent)
          disabled={
             totalCount !== undefined 
               ? (page >= pageCount - 1 && reports.length < PAGE_SIZE) 
               : (reports.length < PAGE_SIZE)
          }
        >
          Next ▶
        </Button>
      </div>
    </div>
  );
};

export default PreviewClientReports;
