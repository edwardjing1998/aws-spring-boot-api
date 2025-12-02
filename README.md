// DuplicateModeButtonPanel.tsx
import React from 'react';
import { Box, Tabs, Tab, Button, SxProps, Theme } from '@mui/material';
import { CRow, CCol } from '@coreui/react';

// Note: These imports were in the original file but appeared unused in the render logic provided.
// Keeping them here to maintain parity with the original file.
import EditSysPrinGeneral from '../sys-prin-config/EditSysPrinGeneral';
import EditReMailOptions from '../sys-prin-config/EditReMailOptions';
import EditStatusOptions from '../sys-prin-config/EditStatusOptions';
import EditFileReceivedFrom from '../sys-prin-config/EditFileReceivedFrom';
import EditFileSentTo from '../sys-prin-config/EditFileSentTo';
import EditSysPrinNotes from '../sys-prin-config/EditSysPrinNotes';
import TwoPagePagination from '../sys-prin-config/TwoPagePagination';

interface DuplicateModeButtonPanelProps {
  mode: string;
  tabIndex: number;
  setTabIndex: (index: number) => void;
  selectedData: any; // Replace 'any' with your specific data interface
  setSelectedData: (data: any) => void;
  isEditable: boolean;
  onChangeGeneral: (...args: any[]) => void;
  statusMap: any;
  setStatusMap: (map: any) => void;
  onChangeVendorReceivedFrom: (...args: any[]) => void;
  onChangeVendorSentTo: (...args: any[]) => void;
  saving: boolean;
  primaryLabel: string;
  sharedSx?: SxProps<Theme>;
  getStatusValue: (...args: any[]) => any;
  handlePrimaryClick: () => void;
}

const DuplicateModeButtonPanel: React.FC<DuplicateModeButtonPanelProps> = ({
  mode,
  tabIndex,
  setTabIndex,
  selectedData,
  setSelectedData,
  isEditable,
  onChangeGeneral,
  statusMap,
  setStatusMap,
  onChangeVendorReceivedFrom,
  onChangeVendorSentTo,
  saving,
  primaryLabel,
  sharedSx,
  getStatusValue,
  handlePrimaryClick
}) => {
  const hasTabs =
    mode === 'duplicate' ||
    mode === 'changeAll' ||
    mode === 'delete' ||
    mode === 'new' ||
    mode === 'edit' ||
    mode === 'move';

  if (!hasTabs) return null;

  return (
    <>
      {/* Tabs */}
      <Tabs
        value={tabIndex}
        onChange={(_, v) => setTabIndex(v)}
        variant="scrollable"
        scrollButtons="auto"
        sx={{ mt: 1, mb: 2 }}
      >
        <Tab
          label={
            <Box sx={{ display: 'flex', alignItems: 'center', gap: 0.5 }}>
              <Box
                sx={{
                  width: 18,
                  height: 18,
                  borderRadius: '50%',
                  backgroundColor: '#1976d2',
                  color: 'white',
                  fontSize: '.7rem',
                  display: 'flex',
                  alignItems: 'center',
                  justifyContent: 'center',
                }}
              >
                1
              </Box>
              Duplicate SysPrin Overview
            </Box>
          }
          sx={{ fontSize: '0.78rem', textTransform: 'none', minWidth: 205, maxWidth: 205, px: 1 }}
        />
      </Tabs>

      {/* Tab Content */}
      <Box sx={{ minHeight: '400px', mt: 2 }}>
        {tabIndex === 0 && (
          <TwoPagePagination
            selectedData={selectedData}
            isEditable={isEditable}
            sharedSx={sharedSx}
            getStatusValue={getStatusValue}
          />
        )}
      </Box>

      {/* Footer buttons */}
      <CRow className="mt-3">
        <CCol style={{ display: 'flex', justifyContent: 'flex-end', gap: '12px' }}>
          {tabIndex === 0 && (
            <Button
              variant="contained"
              size="small"
              onClick={handlePrimaryClick}
              disabled={saving}
            >
              {primaryLabel}
            </Button>
          )}
        </CCol>
      </CRow>
    </>
  );
};

export default DuplicateModeButtonPanel;
