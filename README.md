import { Button, Typography } from '@mui/material';
import React, { useState, useEffect } from 'react';

import {
  previewCellStyle,
  previewHeaderStyle
} from './EditClientReport.styles';

import {
  ClientReportItem,
  PreviewClientReportsProps
} from './EditClientReport.types';

import { fetchPreviewClientReports } from './ClientReport.service';

const PAGE_SIZE = 8; // Match your API call size
const COLUMNS = 2;

const PreviewClientReports: React.FC<PreviewClientReportsProps> = ({
  clientId,
  data,
  reportOptionTotal
}) => {
  // Store page index per client: { "0042": 1, "0043": 0 }
  const [pageMap, setPageMap] = useState<Record<string, number>>({});
  const [reports, setReports] = useState<ClientReportItem[]>(data || []);

  // Get current page for this client, default to 0
  const page = clientId ? (pageMap[clientId] || 0) : 0;

  // Total elements used for pagination
  const totalCount = reportOptionTotal;

  // Helper to update page for specific client
  const setClientPage = (newPage: number) => {
    if (!clientId) return;
    setPageMap((prev) => ({
      ...prev,
      [clientId]: newPage,
    }));
  };

  // Keep local reports in sync when parent provides data for the current client/page (optional optimization)
  useEffect(() => {
    if (data) setReports(data);
  }, [data]);

  // Fetch current page whenever page/clientId changes
  useEffect(() => {
    if (!clientId) return;

    const fetchData = async () => {
      try {
        const result = await fetchPreviewClientReports(clientId, page, PAGE_SIZE);
        setReports(result);
      } catch (e) {
        console.error('fetchPreviewClientReports failed:', e);
        setReports([]);
      }
    };

    fetchData();
  }, [clientId, page]);

  const hasData = reports && reports.length > 0;

  // Calculate total pages
  const pageCount = totalCount ? Math.ceil(totalCount / PAGE_SIZE) : 0;

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
          alignContent: 'start',
        }}
      >
        {/* Header Row */}
        <div style={previewHeaderStyle}>Name</div>
        <div style={previewHeaderStyle}>Received</div>
        <div style={previewHeaderStyle}>Type</div>
        <div style={previewHeaderStyle}>Output</div>

        {/* Data Rows */}
        {hasData ? (
          reports.map((item, index) => (
            <React.Fragment key={`${item.reportId}-${index}`}>
              <div style={previewCellStyle}>{item.reportDetails?.queryName?.trim() || ''}</div>
              <div style={previewCellStyle}>{item.receiveFlag ? 'Yes' : 'No'}</div>
              <div style={previewCellStyle}>{item.reportDetails?.fileExt || ''}</div>
              <div style={previewCellStyle}>{item.outputTypeCd}</div>
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
          alignItems: 'center',
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
          Page {totalCount ? page + 1 : 0} {totalCount !== undefined ? `of ${pageCount}` : ''}
        </Typography>

        <Button
          variant="text"
          size="small"
          sx={{ fontSize: '0.7rem', padding: '2px 8px', minWidth: 'unset', textTransform: 'none' }}
          onClick={() => setClientPage(page + 1)}
          disabled={totalCount !== undefined ? page >= pageCount - 1 : reports.length < PAGE_SIZE}
        >
          Next ▶
        </Button>
      </div>
    </div>
  );
};

export default PreviewClientReports;
