import { Button, Typography } from '@mui/material';
import React, { useState } from 'react';

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

  const safeData = data || [];
  const pageCount = Math.ceil(safeData.length / PAGE_SIZE);
  const pageData = safeData.slice(page * PAGE_SIZE, (page + 1) * PAGE_SIZE);
  const hasData = safeData.length > 0;

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







export interface SysPrinDTO {
  client: string;
  sysPrin: string;
  // plus all other fields from the backend
  [key: string]: any;
}

/**
 * Shape of the data consumed by the UI.
 * We map the backend response to this structure.
 */
export interface SysPrinPageResponse {
  items: SysPrinDTO[];   // list of SysPrins for this page
  page: number;          // current page index (0-based)
  size: number;          // page size
  total: number;         // total number of SysPrins for this client
}

/**
 * Internal interface representing the raw JSON structure from your backend.
 * Based on the sample: { content: [], page: 0, size: 10, totalElements: 18, totalPages: 2 }
 */
interface BackendSysPrinResponse {
  content: SysPrinDTO[];
  page: number;
  size: number;
  totalElements: number;
  totalPages: number;
}

/**
 * Fetch paged SysPrins for a client.
 *
 * GET /sysprins/client/{clientId}/paged?page={page}&size={size}
 */
export async function fetchSysPrinsByClientPaged(
  clientId: string,
  page: number,
  size: number,
): Promise<SysPrinPageResponse> {
  const url = `http://localhost:8089/client-sysprin-reader/api/sysprins/client/${encodeURIComponent(
    clientId,
  )}/paged?page=${encodeURIComponent(page)}&size=${encodeURIComponent(size)}`;

  const resp = await fetch(url, {
    method: 'GET',
    headers: { accept: '*/*' },
  });

  if (!resp.ok) {
    const text = await resp.text().catch(() => '');
    throw new Error(
      `Failed to fetch SysPrins for client ${clientId}, page=${page}, size=${size}: ${resp.status} ${text}`,
    );
  }

  const json = await resp.json();

  // 1. Check for the standard Spring Data / Paged structure provided in your example
  if (json && Array.isArray(json.content)) {
    const typed = json as BackendSysPrinResponse;
    return {
      items: typed.content,
      page: typed.page,
      size: typed.size,
      total: typed.totalElements,
    };
  }

  // 2. Fallback: if backend returns plain array
  if (Array.isArray(json)) {
    const arr = json as SysPrinDTO[];
    return {
      items: arr,
      page,
      size,
      total: arr.length,
    };
  }

  // 3. Fallback: Generic mapping if structure is different but has items/total
  return {
    items: json.items ?? [],
    page: typeof json.page === 'number' ? json.page : page,
    size: typeof json.size === 'number' ? json.size : size,
    total: typeof json.total === 'number' ? json.total : (json.items?.length ?? 0),
  };
}






