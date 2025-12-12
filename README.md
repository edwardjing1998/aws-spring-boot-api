// EditClientReport.styles.tsx

import React from 'react';

export const rowCellStyle: React.CSSProperties = {
  backgroundColor: 'white',
  fontSize: '0.72rem',
  lineHeight: 1.1,
  padding: '2px 6px',
  borderBottom: '1px dotted #ddd',
  display: 'flex',
  alignItems: 'center',
  whiteSpace: 'nowrap',
  overflow: 'hidden',
  textOverflow: 'ellipsis',
};

export const headerCellStyle: React.CSSProperties = {
  backgroundColor: '#f0f0f0',
  fontWeight: 'bold',
  fontSize: '0.75rem',
  padding: '2px 6px',
  borderBottom: '1px dotted #ccc',
};
