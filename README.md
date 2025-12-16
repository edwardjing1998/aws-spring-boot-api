import { Button, Typography } from '@mui/material';
import React, { useState, useEffect } from 'react';
import { fetchSysPrinsByClientPaged } from './utils/SysPrinIntegrationService';

const PAGE_SIZE = 12; // 4x4 grid (displays 2 items per row, 6 rows visible)
const COLUMNS = 4;

export interface SysPrinItem {
  sysPrin: string;
  sysPrinActive?: boolean | string | number; // Supports boolean or '1'/'0'
  [key: string]: any;
}

interface PreviewClientSysPrinListProps {
  data?: SysPrinItem[];
  sysPrinTotal?: number;
}

const PreviewClientSysPrinList: React.FC<PreviewClientSysPrinListProps> = ({ data, sysPrinTotal }) => {
  const [page, setPage] = useState<number>(0);
  const [sysPrins, setSysPrins] = useState<SysPrinItem[]>(data || []);

  // Try to find a clientId from the initial data passed in.
  // Assuming the items contain the 'client' field as per SysPrinDTO.
  const clientId = data && data.length > 0 ? data[0].client : null;

  const totalCount = sysPrinTotal || 0;

  useEffect(() => {
    // If 'data' prop changes (e.g. parent selection changes), update local list
    if (data) {
      setSysPrins(data);
      // Reset page to 0 if data changes (optional but usually desired on context switch)
      // setPage(0); 
    }
  }, [data]);

  // Fetch data when page changes or clientId changes
  useEffect(() => {
    if (!clientId) return;

    // Optimization: If we are on page 0 and the props 'data' is already for this client and page 0, use it.
    if (page === 0 && data && data.length > 0 && data[0].client === clientId) {
      setSysPrins(data);
      return;
    }

    const fetchData = async () => {
      try {
        const response = await fetchSysPrinsByClientPaged(clientId, page, PAGE_SIZE);
        setSysPrins(response.items as SysPrinItem[]);
      } catch (error) {
        console.error("Error fetching sysprins:", error);
        setSysPrins([]);
      }
    };

    fetchData();
  }, [page, clientId, data]);

  const hasData = sysPrins && sysPrins.length > 0;
  
  // Calculate total pages if totalCount is provided
  const pageCount = Math.ceil(totalCount / PAGE_SIZE);

  // Data is now server-side paged, so the "pageData" is just the current state
  const pageData = sysPrins;

  // alert("sysPrinTotal " + sysPrinTotal);

  const cellStyle: React.CSSProperties = {
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

  const headerStyle: React.CSSProperties = {
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
          pageData.reduce<React.ReactNode[]>((rows, item, index) => {
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
          disabled={page === 0}
        >
          ◀ Previous
        </Button>

        <Typography fontSize="0.75rem">
          Page {totalCount ? page + 1 : 0} of {pageCount}
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

export default PreviewClientSysPrinList;
