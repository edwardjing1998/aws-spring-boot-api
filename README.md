import React, { useState, useEffect, useRef } from 'react';
import Autocomplete from '@mui/material/Autocomplete';
import TextField from '@mui/material/TextField';
import Popper from '@mui/material/Popper';
import SearchIcon from '@mui/icons-material/Search';
import InputAdornment from '@mui/material/InputAdornment';

import { fetchClientSuggestions } from '../modules/edit/client-information/utils/ClientIntegrationService';

const ClientAutoCompleteInputBox = ({ inputValue, setInputValue, onClientsFetched, isWildcardMode, setIsWildcardMode }) => {
  const [options, setOptions] = useState([]);
  const [selectedValue, setSelectedValue] = useState(null);
  const keywordRef = useRef('');

  const CustomPopper = (props) => (
    <Popper
      {...props}
      modifiers={[{ name: 'offset', options: { offset: [0, 4] } }]}
      style={{ width: 400 }}
    />
  );

  useEffect(() => {
    if (!isWildcardMode) setInputValue('');
  }, [isWildcardMode]);

  useEffect(() => {
    const delay = setTimeout(() => {
      const kw = inputValue.trim();
      if (!kw) return setOptions([]);

      fetchClientSuggestions(kw)
        .then((data) => {
          const list = data.data || data;      
          setOptions(list);
          if (kw.endsWith('*') && typeof onClientsFetched === 'function') {
            onClientsFetched(list);
          }

          setIsWildcardMode?.(kw.endsWith('*'));
        })
        .catch((err) => {
          console.error('Autocomplete fetch error:', err);
          setOptions([]);
        });
    }, 300);

    return () => clearTimeout(delay);
  }, [inputValue]);  

  return (
    <Autocomplete
      inputValue={inputValue}
      freeSolo
      disableClearable
      fullWidth
      options={options
        .map((opt) =>
          typeof opt === 'object' && opt.client && opt.name ? `${opt.client} - ${opt.name}` : ''
        )
        .filter(Boolean)}
      onInputChange={(_, v) => setInputValue(v)}
      onChange={(_, v) => setSelectedValue(v)}
      PopperComponent={CustomPopper}
      slotProps={{
        paper: { sx: { fontSize: '0.78rem', width: 300 } },
        listbox: { sx: { fontSize: '0.78rem' } },
      }}
      sx={{ width: '100%', backgroundColor: '#fff' }}
      renderInput={(params) => (
        <TextField
          {...params}
          placeholder="Search Client"
          variant="outlined"
          fullWidth
          size="small"
          InputProps={{
            ...params.InputProps,
            type: 'search',
            endAdornment: (
              <InputAdornment position="end">
                <SearchIcon sx={{ fontSize: 18, color: '#555' }} />
              </InputAdornment>
            ),
            sx: {
              height: 36,
              p: '0 8px',
              fontSize: '0.875rem',
              backgroundColor: '#fff',
              '& .MuiOutlinedInput-notchedOutline': { border: '1px solid black' },
              '&:hover .MuiOutlinedInput-notchedOutline': { border: '1px solid black' },
              '&.Mui-focused .MuiOutlinedInput-notchedOutline': { border: '1px solid black' },
            },
          }}
        />
      )}
    />
  );
};

export default ClientAutoCompleteInputBox;



curl -X 'GET' \
  'http://localhost:8089/client-sysprin-reader/api/sysprins/detail/0003/28282829' \
  -H 'accept: application/json'



  [
  {
    "client": "0003",
    "name": "0003 client name",
    "addr": "0003 address line",
    "city": "king of prussia",
    "state": "AK",
    "zip": "19765",
    "contact": "0003 contact",
    "phone": "415-432-4322",
    "active": true,
    "faxNumber": "543-267-6544",
    "billingSp": "198754",
    "reportBreakFlag": 0,
    "chLookUpType": 0,
    "excludeFromReport": true,
    "positiveReports": true,
    "subClientInd": true,
    "subClientXref": "5624",
    "amexIssued": true,
    "reportOptions": [
      {
        "clientId": "0003",
        "reportId": 191,
        "receiveFlag": true,
        "outputTypeCd": 1,
        "fileTypeCd": 1,
        "emailFlag": 1,
        "emailBodyTx": "email body 191",
        "reportPasswordTx": "725667",
        "reportDetails": {
          "reportId": 191,
          "queryName": "Binary - Rapid Daily Activity Report updated      ",
          "query": "Exec USP_ClientDailyActivity_6056 '6056'",
          "inputDataFields": "Client/Start Date/End Date                                                                                                                                                                                                                                     ",
          "fileExt": "TXT",
          "dbDriverType": "{SQL SERVER}                  ",
          "fileHeaderInd": 1,
          "defaultFileNm": "Binary",
          "reportDbServer": "W3DVDS1051",
          "reportDb": "Rapid3",
          "reportDbUserid": "svc-fdpd-tag",
          "reportDbPasswrd": "D9EE541990967B8D12753C8CD5A6E946",
          "fileTransferType": 1,
          "reportDbIpAndPort": "",
          "reportByClientFlag": false,
          "emailFromAddress": "rapid.reports@firstdatacorp.com",
          "emailEventId": "                                                  ",
          "tabDelimitedFlag": false,
          "inputFileTx": "",
          "inputFileKeyStartPos": 0,
          "inputFileKeyLength": 0,
          "accessLevel": 6,
          "isActive": true,
          "isVisible": true
        }
      }
    ],
    "sysPrinsPrefixes": [
      {
        "billingSp": "198754",
        "prefix": "123456",
        "atmCashRule": "0"
      }
    ],
    "clientEmail": [
      {
        "clientId": "0003",
        "emailAddressTx": "0003.01@gmail.com",
        "reportId": 110,
        "emailNameTx": "0003 name 01",
        "carbonCopyFlag": true,
        "activeFlag": true,
        "mailServerId": 0
      }
    ],
    "sysPrins": [
      {
        "client": "0003",
        "sysPrin": "12121898",
        "custType": "1",
        "undeliverable": "1",
        "statA": "0",
        "statB": "0",
        "statC": "1",
        "statD": "1",
        "statE": "0",
        "statF": "0",
        "statI": "0",
        "statL": "0",
        "statO": "0",
        "statU": "0",
        "statX": "0",
        "statZ": "0",
        "poBox": "1",
        "addrFlag": "1",
        "tempAway": 1234,
        "rps": "1",
        "session": "",
        "badState": "1",
        "astatRch": "1",
        "nm13": "1",
        "tempAwayAtts": 12345,
        "reportMethod": 0,
        "sysPrinActive": true,
        "notes": "Notes for 38383838 updated updated 12121813 updated updated updated 888  for 87878787 87878787 this is new updated new update",
        "returnStatus": "A",
        "destroyStatus": "1",
        "nonUS": "1",
        "special": "1",
        "pinMailer": "1",
        "holdDays": 1145,
        "forwardingAddress": "1",
        "sysPrinContact": "",
        "sysPrinPhone": "",
        "entityCode": "0",
        "vendorSentTo": [
          {
            "sysPrin": "12121898",
            "vendorId": "v04",
            "queForMail": false,
            "vendor": {
              "id": "v04",
              "name": "Remail Memo Rejects                               ",
              "active": true,
              "receiver": false,
              "fileServerName": "tafmsas2                                ",
              "fileServerIp": "               ",
              "fileServerUserId": "ndmfrd0   ",
              "fileServerPassword": "0718CF1F9445528370DDFE84CA612E9D                  ",
              "fileName": "FWRreject.OUT                                     ",
              "uncShare": "Transferdata                                                                                                                                                                                                                                                   ",
              "path": "Rapid                                             ",
              "archivePath": "Rapid\\Archive                                     ",
              "vendorTypeCode": "R",
              "fileIo": "I",
              "clientSpecific": false,
              "schedule": "10:30:00",
              "dateLastWorked": "2015/09/08",
              "fileType": 3
            }
          }
        ],
        "vendorReceivedFrom": [
          {
            "sysPrin": "12121898",
            "vendorId": "v09",
            "queForMail": false,
            "vendor": {
              "id": "v09",
              "name": "Solutions Send                                    ",
              "active": true,
              "receiver": true,
              "fileServerName": "tafmsas2                                ",
              "fileServerIp": "               ",
              "fileServerUserId": "ndmfrd0   ",
              "fileServerPassword": "0718CF1F9445528370DDFE84CA612E9D                  ",
              "fileName": "SOLU.TXT                                          ",
              "uncShare": "Transferdata                                                                                                                                                                                                                                                   ",
              "path": "RAPID\\Solutions\\Output\\RFIIRA2I                   ",
              "vendorTypeCode": "A",
              "fileIo": "O",
              "clientSpecific": false,
              "schedule": "10:30:00",
              "dateLastWorked": "2015/09/07",
              "fileSize": 156,
              "fileType": 3
            }
          }
        ]
      },
      {
        "client": "0003",
        "sysPrin": "28282829",
        "custType": "1",
        "undeliverable": "1",
        "statA": "0",
        "statB": "0",
        "statC": "1",
        "statD": "1",
        "statE": "0",
        "statF": "0",
        "statI": "0",
        "statL": "0",
        "statO": "0",
        "statU": "0",
        "statX": "0",
        "statZ": "0",
        "poBox": "1",
        "addrFlag": "0",
        "tempAway": 1234,
        "rps": "0",
        "session": " ",
        "badState": "1",
        "astatRch": "0",
        "nm13": "0",
        "tempAwayAtts": 12345,
        "reportMethod": 0,
        "sysPrinActive": false,
        "notes": "Notes for 38383838 updated updated 12121813 updated updated updated 888  for 87878787 87878787 this is new updated",
        "returnStatus": "A",
        "destroyStatus": "1",
        "nonUS": "1",
        "special": "1",
        "pinMailer": "1",
        "holdDays": 1145,
        "forwardingAddress": "1",
        "sysPrinContact": "",
        "sysPrinPhone": "",
        "entityCode": "0",
        "vendorSentTo": [
          {
            "sysPrin": "28282829",
            "vendorId": "v04",
            "queForMail": false,
            "vendor": {
              "id": "v04",
              "name": "Remail Memo Rejects                               ",
              "active": true,
              "receiver": false,
              "fileServerName": "tafmsas2                                ",
              "fileServerIp": "               ",
              "fileServerUserId": "ndmfrd0   ",
              "fileServerPassword": "0718CF1F9445528370DDFE84CA612E9D                  ",
              "fileName": "FWRreject.OUT                                     ",
              "uncShare": "Transferdata                                                                                                                                                                                                                                                   ",
              "path": "Rapid                                             ",
              "archivePath": "Rapid\\Archive                                     ",
              "vendorTypeCode": "R",
              "fileIo": "I",
              "clientSpecific": false,
              "schedule": "10:30:00",
              "dateLastWorked": "2015/09/08",
              "fileType": 3
            }
          }
        ],
        "vendorReceivedFrom": [
          {
            "sysPrin": "28282829",
            "vendorId": "v09",
            "queForMail": false,
            "vendor": {
              "id": "v09",
              "name": "Solutions Send                                    ",
              "active": true,
              "receiver": true,
              "fileServerName": "tafmsas2                                ",
              "fileServerIp": "               ",
              "fileServerUserId": "ndmfrd0   ",
              "fileServerPassword": "0718CF1F9445528370DDFE84CA612E9D                  ",
              "fileName": "SOLU.TXT                                          ",
              "uncShare": "Transferdata                                                                                                                                                                                                                                                   ",
              "path": "RAPID\\Solutions\\Output\\RFIIRA2I                   ",
              "vendorTypeCode": "A",
              "fileIo": "O",
              "clientSpecific": false,
              "schedule": "10:30:00",
              "dateLastWorked": "2015/09/07",
              "fileSize": 156,
              "fileType": 3
            }
          }
        ]
      },
      {
        "client": "0003",
        "sysPrin": "43434343",
        "custType": "1",
        "undeliverable": "1",
        "statA": "0",
        "statB": "0",
        "statC": "1",
        "statD": "1",
        "statE": "0",
        "statF": "0",
        "statI": "0",
        "statL": "0",
        "statO": "0",
        "statU": "0",
        "statX": "0",
        "statZ": "0",
        "poBox": "1",
        "addrFlag": "0",
        "tempAway": 1234,
        "rps": "0",
        "session": " ",
        "badState": "1",
        "astatRch": "0",
        "nm13": "0",
        "tempAwayAtts": 12345,
        "reportMethod": 0,
        "sysPrinActive": false,
        "notes": "Notes for 38383838 updated updated 12121813 updated updated updated 888  for 87878787 87878787 this is new updated",
        "returnStatus": "A",
        "destroyStatus": "1",
        "nonUS": "1",
        "special": "1",
        "pinMailer": "1",
        "holdDays": 1145,
        "forwardingAddress": "1",
        "sysPrinContact": "",
        "sysPrinPhone": "",
        "entityCode": "0"
      }
    ]
  }
]



