import { Button, Typography } from '@mui/material';
import React, { useState, useEffect } from 'react';

const PAGE_SIZE = 8; // 4x4 grid
const COLUMNS = 2;

const PreviewClientReports = ({ data, reportOptionTotal }) => {
  const [page, setPage] = useState(0);

  // alert("totalCount " + reportOptionTotal);

  const pageCount = Math.ceil((data?.length || 0) / PAGE_SIZE);
  const pageData = data?.slice(page * PAGE_SIZE, (page + 1) * PAGE_SIZE) || [];

  useEffect(() => {
    if (data && data.length > 0) {
      console.info(JSON.stringify(data, null, 2));
    }
  }, [data]);

  const hasData = data && data.length > 0;

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

        {/* Data Rows */}
        {pageData.length > 0 ? (
          pageData.map((item, index) => (
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

export default PreviewClientReports;



curl -X 'GET' \
  'http://localhost:8089/client-sysprin-reader/api/client/report-option/0042?page=0&size=8' \
  -H 'accept: */*'


[
  {
    "clientId": "0042",
    "reportId": 177,
    "receiveFlag": true,
    "outputTypeCd": 2,
    "fileTypeCd": 1,
    "emailFlag": 1,
    "emailBodyTx": "this is report 177 for 0042",
    "reportPasswordTx": "725867",
    "reportDetails": {
      "reportId": 177,
      "queryName": "Custom Excel Report                               ",
      "query": "USP_CUSTOM_EXCEL_REPORT '<RunDate>'",
      "inputDataFields": "RunDate                                                                                                                                                                                                                                                        ",
      "fileExt": "XLS",
      "dbDriverType": "{SQL SERVER}                  ",
      "fileHeaderInd": 0,
      "defaultFileNm": "",
      "reportDbServer": "W3DVDS1051",
      "reportDb": "Rapid3",
      "reportDbUserid": "",
      "reportDbPasswrd": "",
      "fileTransferType": 2,
      "reportDbIpAndPort": "",
      "reportByClientFlag": false,
      "rerunDateRangeStart": null,
      "rerunDateRangeEnd": null,
      "rerunClientId": "    ",
      "emailFromAddress": "",
      "emailEventId": "                                                  ",
      "tabDelimitedFlag": false,
      "inputFileTx": "D:\\BatchReportData\\Input\\CustomExcelReport.txt",
      "inputFileKeyStartPos": 0,
      "inputFileKeyLength": 14,
      "accessLevel": 1,
      "isActive": false,
      "isVisible": false,
      "numSheets": 0,
      "c3FileTransfer": null
    }
  },
  {
    "clientId": "0042",
    "reportId": 190,
    "receiveFlag": true,
    "outputTypeCd": 1,
    "fileTypeCd": 1,
    "emailFlag": 1,
    "emailBodyTx": "this is report 190 for 0042",
    "reportPasswordTx": "725867",
    "reportDetails": {
      "reportId": 190,
      "queryName": "First Data Rapid Daily Activity Web Report HSBC   ",
      "query": "Exec USP_ClientDailyActivityHSBC '0069'",
      "inputDataFields": "'StartDate', 'EndDate'                                                                                                                                                                                                                                         ",
      "fileExt": "TXT",
      "dbDriverType": "{SQL SERVER}                  ",
      "fileHeaderInd": 1,
      "defaultFileNm": "CL0069",
      "reportDbServer": "W3DVDS1051",
      "reportDb": "Rapid3",
      "reportDbUserid": "svc-fdpd-tag",
      "reportDbPasswrd": "D9EE541990967B8D12753C8CD5A6E946",
      "fileTransferType": 2,
      "reportDbIpAndPort": "0",
      "reportByClientFlag": null,
      "rerunDateRangeStart": null,
      "rerunDateRangeEnd": null,
      "rerunClientId": null,
      "emailFromAddress": " ",
      "emailEventId": "                                                  ",
      "tabDelimitedFlag": null,
      "inputFileTx": null,
      "inputFileKeyStartPos": null,
      "inputFileKeyLength": null,
      "accessLevel": 1,
      "isActive": true,
      "isVisible": true,
      "numSheets": 0,
      "c3FileTransfer": null
    }
  }]



