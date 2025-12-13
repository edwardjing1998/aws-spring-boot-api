import { Button, Typography } from '@mui/material';
import React, { useState, useEffect } from 'react';

const PAGE_SIZE = 10;
const COLUMNS = 4; // Adjusted to match the rendered columns (Prefix, Rule, Prefix, Rule)

const PreviewAtmAndCashPrefixes = ({ data, clientPrefixTotal }) => {
  const [page, setPage] = useState(0);
  const [prefixes, setPrefixes] = useState(data || []);

  // Attempt to find clientId (billingSp) from data prop if available
  // Assuming the API returns objects with a 'billingSp' or similar ID field.
  // If 'data' is initially empty, we might need another way to get clientId.
  // Based on your curl example, '34220000' seems to be the ID.
  // Ideally this component should receive 'clientId' as a prop.
  // Falling back to check the first item in data.
  const clientId = data && data.length > 0 ? (data[0].billingSp || data[0].clientId) : null;
  
  // Use clientPrefixTotal prop for total count
  const totalCount = clientPrefixTotal || 0;
  
  useEffect(() => {
    if (data) {
      setPrefixes(data);
      // Reset page when parent data refreshes (optional, but standard for new parent selection)
      // setPage(0); 
    }
  }, [data]);

  // Fetch data when page changes
  useEffect(() => {
    if (!clientId) return;

    // Optimization: If on page 0 and we have initial data matching this client, use it.
    if (page === 0 && data && data.length > 0 && (data[0].billingSp === clientId || data[0].clientId === clientId)) {
        setPrefixes(data);
        return;
    }

    const fetchData = async () => {
      try {
        const url = `http://localhost:8089/client-sysprin-reader/api/client/sysprin-prefix/${encodeURIComponent(clientId)}/pagination?page=${page}&size=${PAGE_SIZE}`;
        
        const resp = await fetch(url, {
          method: 'GET',
          headers: { accept: '*/*' },
        });

        if (resp.ok) {
          const json = await resp.json();
          // Assuming API returns array directly or { content: [] } based on previous patterns.
          // Adjust logic here if response shape differs. 
          // Based on previous files, let's assume direct array or standard Spring Page.
          const newPrefixes = Array.isArray(json) ? json : (json.content || []);
          setPrefixes(newPrefixes);
        } else {
          console.error("Failed to fetch prefixes");
          setPrefixes([]);
        }
      } catch (error) {
        console.error("Error fetching prefixes:", error);
        setPrefixes([]);
      }
    };

    fetchData();
  }, [page, clientId, data]); // removed data dependency to avoid loop if not careful, added logic check instead

  const hasData = prefixes && prefixes.length > 0;
  
  // Calculate total pages
  const pageCount = Math.ceil(totalCount / PAGE_SIZE);

  // Data is now server-side paged, so the "pageData" is just the current state
  const pageData = prefixes;

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
    borderBottom: '1px dashed #ddd'
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
          minHeight: '100px',
          alignContent: 'start'
        }}
      >
        {/* Header Row */}
        <div style={headerStyle}>Prefix</div>
        <div style={headerStyle}>Rule</div>
        <div style={headerStyle}>Prefix</div>
        <div style={headerStyle}>Rule</div>

        {/* Data Rows */}
        {hasData ? (
          pageData.reduce((rows, item, index) => {
            if (index % 2 === 0) {
              const second = pageData[index + 1];
              rows.push(
                <React.Fragment key={index}>
                  <div style={cellStyle}>{item.prefix} : </div>
                  <div style={cellStyle}>{item.atmCashRule === '1' ? 'Return' : 'Destroy'}</div>
                  <div style={cellStyle}>{second?.prefix || ''} {second ? ':' : ''} </div>
                  <div style={cellStyle}>{second ? (second.atmCashRule === '1' ? 'Return' : 'Destroy') : ''}</div>
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
          disabled={page === 0}
        >
          ◀ Previous
        </Button>

        <Typography fontSize="0.75rem">
          Page {page + 1} of {pageCount}
        </Typography>

        <Button
          variant="text"
          size="small"
          sx={{ fontSize: '0.7rem', padding: '2px 8px', minWidth: 'unset', textTransform: 'none' }}
          onClick={() => setPage((p) => Math.min(p + 1, pageCount - 1))}
          disabled={page >= pageCount - 1}
        >
          Next ▶
        </Button>
      </div>
    </div>
  );
};

export default PreviewAtmAndCashPrefixes;
