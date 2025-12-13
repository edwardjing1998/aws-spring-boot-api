import { Button, Typography } from '@mui/material';
import React, { useState, useEffect, useRef } from 'react';

// 1. Import shared types
import { AtmCashPrefixRow } from './EditAtmCashPrefix.types';
// 2. Import the service function
import { fetchPreviewAtmCashPrefixes } from './utils/PreviewAtmAndCashPrefixes.service';

const PAGE_SIZE = 10;
const COLUMNS = 4; 

// Using shared type for data items
interface PreviewAtmAndCashPrefixesProps {
  data?: AtmCashPrefixRow[];
  clientPrefixTotal?: number;
}

const PreviewAtmAndCashPrefixes: React.FC<PreviewAtmAndCashPrefixesProps> = ({ data, clientPrefixTotal }) => {
  // Changed: Use pageMap to persist page state per client { "clientId": pageIndex }
  const [pageMap, setPageMap] = useState<Record<string, number>>({});
  const [prefixes, setPrefixes] = useState<AtmCashPrefixRow[]>(data || []);

  // Attempt to find clientId (billingSp) from data prop if available
  const clientId = data && data.length > 0 ? (data[0].billingSp || data[0].clientId) : null;
  
  // Changed: Derive current page from map, defaulting to 0
  const page = clientId ? (pageMap[clientId] || 0) : 0;

  // Use clientPrefixTotal prop for total count
  const totalCount = clientPrefixTotal || 0;

  // Changed: Helper to update page for specific client
  const setClientPage = (newPage: number) => {
    if (!clientId) return;
    setPageMap(prev => ({
        ...prev,
        [clientId]: newPage
    }));
  };
  
  useEffect(() => {
    if (data) {
      setPrefixes(data);
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
      // 3. Call the service function
      const result = await fetchPreviewAtmCashPrefixes(clientId, page, PAGE_SIZE);
      setPrefixes(result);
    };

    fetchData();
  }, [page, clientId, data]); 

  const hasData = prefixes && prefixes.length > 0;
  
  // Calculate total pages
  const pageCount = Math.ceil(totalCount / PAGE_SIZE);

  // Data is now server-side paged, so the "pageData" is just the current state
  const pageData = prefixes;

  const cellStyle = (isSelected: boolean = false): React.CSSProperties => ({
    backgroundColor: isSelected ? '#cce5ff' : 'white',
    minHeight: '25px',
    display: 'flex',
    alignItems: 'center',
    justifyContent: 'flex-start',
    fontSize: '0.78rem',
    fontWeight: 200,
    padding: '0 10px',
    borderRadius: '0px',
    borderBottom: '1px dashed #ddd'
  });

  const headerStyle: React.CSSProperties = {
    ...cellStyle(false),
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
          pageData.reduce<React.ReactNode[]>((rows, item, index) => {
            if (index % 2 === 0) {
              const second = pageData[index + 1];
              rows.push(
                <React.Fragment key={index}>
                  <div style={cellStyle()}>{item.prefix} : </div>
                  <div style={cellStyle()}>{item.atmCashRule === '1' ? 'Return' : 'Destroy'}</div>
                  <div style={cellStyle()}>{second?.prefix || ''} {second ? ':' : ''} </div>
                  <div style={cellStyle()}>{second ? (second.atmCashRule === '1' ? 'Return' : 'Destroy') : ''}</div>
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
          onClick={() => setClientPage(Math.max(page - 1, 0))}
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
          onClick={() => setClientPage(Math.min(page + 1, pageCount - 1))}
          disabled={page >= pageCount - 1}
        >
          Next ▶
        </Button>
      </div>
    </div>
  );
};

export default PreviewAtmAndCashPrefixes;
