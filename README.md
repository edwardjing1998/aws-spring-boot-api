import React, { useState } from 'react';
import { Tabs, Tab, Box } from '@mui/material';
import PreviewFilesReceivedFrom from '../vendor/PreviewFilesReceivedFrom';
import PreviewSysPrinNotes from './PreviewSysPrinNotes';
import PreviewFilesSentTo from '../vendor/PreviewFilesSentTo';
import PreviewStatusOptions from '../options/PreviewStatusOptions';
import PreviewGeneralInformation from '../components/PreviewGeneralInformation';
import PreviewReMailOptions from '../options/PreviewReMailOptions';


const PreviewSysPrinInformation = ({ setSysPrinInformationWindow, selectedData, selectedGroupRow }) => {
  const [isEditable] = useState(false);
  const [tabIndex, setTabIndex] = useState(0);

  const handleTabChange = (event, newValue) => {
    setTabIndex(newValue);
    if (newValue === 5 && typeof setSysPrinInformationWindow === 'function') {
      setSysPrinInformationWindow(true);
    }
  };

  const getStatusValue = (options, code) => {
    const index = parseInt(code, 10);
    return !isNaN(index) ? options[index] || '' : '';
  };

  const sharedSx = {
    '& .MuiInputBase-root': {
      height: '20px',
      fontSize: '0.75rem'
    },
    '& .MuiInputBase-input': {
      padding: '4px 4px',
      height: '30px',
      fontSize: '0.75rem',
      lineHeight: '1rem'
    },
    '& .MuiInputLabel-root': {
      fontSize: '0.75rem',
      lineHeight: '1rem'
    },
    '& .MuiInputBase-input.Mui-disabled': {
      color: 'black',
      WebkitTextFillColor: 'black'
    },
    '& .MuiInputLabel-root.Mui-disabled': {
      color: 'black'
    }
  };

  const tabs = [
    { label: 'General Info' },
    { label: 'Status Options' },
    { label: 'Re-Mail Options' },
    { label: 'Files Sent To' },
    { label: 'Files Received From' },
    { label: 'Notes' }
  ];

  return (
    <>
      <Box sx={{ borderBottom: 1, borderColor: 'divider', marginBottom: '10px' }}>
      <Tabs value={tabIndex} onChange={handleTabChange}>
          {tabs.map((tab, index) => (
            <Tab
              key={index}
              label={tab.label}
              sx={{ textTransform: 'none' }} // 👈 removes uppercase
            />
          ))}
        </Tabs>
      </Box>

      {tabIndex === 0 && (
        <PreviewGeneralInformation
          selectedData={selectedData}
          isEditable={isEditable}
          sharedSx={sharedSx}
          getStatusValue={getStatusValue}
        />
      )}

      {tabIndex === 1 && (
            <PreviewStatusOptions
              selectedData={selectedData}
              sharedSx={sharedSx}
              getStatusValue={getStatusValue}
            />
      )}


      {tabIndex === 2 && (
        <PreviewReMailOptions
          selectedData={selectedData}
          sharedSx={sharedSx}
        />
      )}

      {tabIndex === 3 && <PreviewFilesSentTo data={selectedData?.vendorSentTo || []} />}

      {tabIndex === 4 && <PreviewFilesReceivedFrom data={selectedData?.vendorReceivedFrom || []} />}

      {tabIndex === 5 && <PreviewSysPrinNotes data={selectedData || []} />}
    </>
  );
};

export default PreviewSysPrinInformation;
