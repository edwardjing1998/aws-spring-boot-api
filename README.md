import { Button, Typography } from '@mui/material';
import React, { useState, useEffect } from 'react';

// --- Types ---

export interface ClientEmailItem {
  clientId?: string | number;
  emailNameTx?: string;
  emailAddressTx?: string;
  // Optional properties that appeared in commented-out code
  receiveFlag?: boolean | number;
  outputTypeCd?: string | number;
  [key: string]: any; // Allow other properties
}

export interface PreviewClientEmailsProps {
  data: ClientEmailItem[] | null | undefined;
  clientEmailTotal?: number;
}

const PAGE_SIZE = 6; // 4x4 grid
const COLUMNS = 4;

const PreviewClientEmails: React.FC<PreviewClientEmailsProps> = ({ 
  data, 
  clientEmailTotal 
}) => {
  const [page, setPage] = useState<number>(0);
  
  // Safe access to length
  const pageCount = Math.ceil((data?.length || 0) / PAGE_SIZE);
  const pageData = data?.slice(page * PAGE_SIZE, (page + 1) * PAGE_SIZE) || [];

  useEffect(() => {
    if (data && data.length > 0) {
      console.info(JSON.stringify(data, null, 2));
    }
  }, [data]);

  const hasData = !!(data && data.length > 0);

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
    borderBottom: '1px dashed #ddd'
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
          gridTemplateColumns: '400px 380px',
          rowGap: '0px',
          columnGap: '4px',
          minHeight: '100px',
          alignContent: 'start'
        }}
      >
        {/* Header Row */}
        <div style={headerStyle}>Name</div>
        {/*<div style={headerStyle}>Received</div>*/}
        <div style={headerStyle}>Email Address</div>
        {/* <div style={headerStyle}>Output</div>*/}

        {/* Data Rows */}
        {pageData.length > 0 ? (
          pageData.map((item, index) => (
            <React.Fragment key={`${item.clientId || 'row'}-${index}`}>
              <div style={cellStyle}>
                {item.emailNameTx?.trim() || ''}
              </div>
              {/* <div style={cellStyle}>{item.receiveFlag ? 'Yes' : 'No'}</div>*/}
              <div style={cellStyle}>
                {item.emailAddressTx || ''}
              </div>
              {/* <div style={cellStyle}>{item.outputTypeCd}</div>*/}
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

export default PreviewClientEmails;
