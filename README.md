import React from 'react';
import { Box, Tabs, Tab, Button, SxProps, Theme } from '@mui/material';
import { CRow, CCol } from '@coreui/react';

import EditSysPrinGeneral    from '../sys-prin-config/EditSysPrinGeneral';
import EditReMailOptions     from '../sys-prin-config/EditReMailOptions';
import EditStatusOptions     from '../sys-prin-config/EditStatusOptions';
import EditFileReceivedFrom from '../sys-prin-config/EditFileReceivedFrom';
import EditFileSentTo        from '../sys-prin-config/EditFileSentTo';
import EditSysPrinNotes      from '../sys-prin-config/EditSysPrinNotes';
import TwoPagePagination     from '../sys-prin-config/TwoPagePagination';

interface DeleteModeButtonPanelProps {
  mode: string;
  tabIndex: number;
  setTabIndex: React.Dispatch<React.SetStateAction<number>>;
  selectedData: any;
  setSelectedData: React.Dispatch<React.SetStateAction<any>>;
  isEditable: boolean;
  onChangeGeneral: (field: string, value: any) => void;
  statusMap: any;
  setStatusMap: React.Dispatch<React.SetStateAction<any>>;
  onChangeVendorReceivedFrom?: (val: any) => void;
  onChangeVendorSentTo?: (val: any) => void;
  saving?: boolean;
  primaryLabel?: string;
  sharedSx?: SxProps<Theme>;
  getStatusValue?: (options: string[], code: string | number | undefined) => string;
  handlePrimaryClick: () => void;
}

const DeleteModeButtonPanel: React.FC<DeleteModeButtonPanelProps> = ({
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
  saving = false,
  primaryLabel = 'Save',
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
              General
            </Box>
          }
          sx={{ fontSize: '0.78rem', textTransform: 'none', minWidth: 205, maxWidth: 205, px: 1 }}
        />

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
                2
              </Box>
              Remail Options
            </Box>
          }
          sx={{ fontSize: '0.78rem', textTransform: 'none', minWidth: 205, maxWidth: 205, px: 1 }}
        />

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
                3
              </Box>
              Status Options
            </Box>
          }
          sx={{ fontSize: '0.78rem', textTransform: 'none', minWidth: 205, maxWidth: 205, px: 1 }}
        />

      {/* Tab
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
                4
              </Box>
              File Received From
            </Box>
          }
          sx={{ fontSize: '0.78rem', textTransform: 'none', minWidth: 205, maxWidth: 205, px: 1 }}
        />

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
                5
              </Box>
              File Sent To
            </Box>
          }
          sx={{ fontSize: '0.78rem', textTransform: 'none', minWidth: 205, maxWidth: 205, px: 1 }}
        />
      */}
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
                6
              </Box>
              SysPrin Note
            </Box>
          }
          sx={{ fontSize: '0.78rem', textTransform: 'none', minWidth: 205, maxWidth: 205, px: 1 }}
        />

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
                7
              </Box>
              Submission Overview
            </Box>
          }
          sx={{ fontSize: '0.78rem', textTransform: 'none', minWidth: 205, maxWidth: 205, px: 1 }}
        />
      </Tabs>

      {/* Tab Content */}
      <Box sx={{ minHeight: '400px', mt: 2 }}>
        {tabIndex === 0 && (
          <EditSysPrinGeneral
            key={`general-${selectedData?.sysPrin ?? ''}`}
            selectedData={selectedData}
            setSelectedData={setSelectedData}
            isEditable={isEditable}
            onChangeGeneral={onChangeGeneral}
          />
        )}

        {tabIndex === 1 && (
          <EditReMailOptions
            key={`remail-${selectedData?.sysPrin ?? ''}`}
            selectedData={selectedData}
            setSelectedData={setSelectedData}
            isEditable={isEditable}
            onChangeGeneral={onChangeGeneral}
          />
        )}

        {tabIndex === 2 && (
          <EditStatusOptions
            selectedData={selectedData}
            statusMap={statusMap}
            setStatusMap={setStatusMap}
            isEditable={isEditable}
            onChangeGeneral={onChangeGeneral}
          />
        )}

        {tabIndex === 3 && (
          <EditFileReceivedFrom
            key={`received-from-${selectedData?.sysPrin ?? ''}`}
            selectedData={selectedData}
            isEditable={isEditable}
            onChangeVendorReceivedFrom={onChangeVendorReceivedFrom}
            setSelectedData={setSelectedData}
          />
        )}

        {tabIndex === 4 && (
          <EditFileSentTo
            key={`sent-to-${selectedData?.sysPrin ?? ''}`}
            selectedData={selectedData}
            isEditable={isEditable}
            onChangeVendorSentTo={onChangeVendorSentTo}
            setSelectedData={setSelectedData}
          />
        )}

        {tabIndex === 5 && (
          <EditSysPrinNotes
            selectedData={selectedData}
            isEditable={isEditable}
            onChangeGeneral={onChangeGeneral}
            // Removed setSelectedData to match expected props and prevent errors
          />
        )}

        {tabIndex === 6 && (
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
        <CCol style={{ display: 'flex', justifyContent: 'flex-start' }}>
          <Button
            variant="outlined"
            size="small"
            onClick={() => setTabIndex((i) => Math.max(i - 1, 0))}
          >
            Back
          </Button>
        </CCol>

        <CCol style={{ display: 'flex', justifyContent: 'flex-end', gap: '12px' }}>
          {tabIndex === 4 && (
            <Button
              variant="contained"
              size="small"
              onClick={handlePrimaryClick}
              disabled={saving}
            >
              {primaryLabel}
            </Button>
          )}

          <Button
            variant="outlined"
            size="small"
            onClick={() => setTabIndex((i) => Math.min(i + 1, 6))}
          >
            Next
          </Button>
        </CCol>
      </CRow>
    </>
  );
};

export default DeleteModeButtonPanel;
