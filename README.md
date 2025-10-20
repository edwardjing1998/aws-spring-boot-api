// SysPrinEditButtonPanel.jsx
import React from 'react';
import { CCard, CCardBody } from '@coreui/react';
import { Button } from '@mui/material';

const SysPrinEditButtonPanel = ({ setSysPrinInformationWindow }) => {
  const openWin = (mode) => setSysPrinInformationWindow({ open: true, mode });

  return (
    <CCard style={{ height: '35px', marginBottom: '4px', marginTop: '25px' }}>
      <CCardBody className="d-flex align-items-center" style={{ padding: '0.25rem 0.5rem', height: '100%' }}>
        <div>
          <Button variant="outlined" onClick={() => openWin('changeAll')}
            size="small" sx={{ fontSize: '0.78rem', mr: '6px', textTransform: 'none' }}>
            Change All
          </Button>

          <Button variant="outlined" onClick={() => openWin('edit')}
            size="small" sx={{ fontSize: '0.78rem', mr: '6px', textTransform: 'none' }}>
            Edit SysPrin
          </Button>

          <Button variant="outlined" onClick={() => openWin('new')}
            size="small" sx={{ fontSize: '0.78rem', mr: '6px', textTransform: 'none' }}>
            New SysPrin
          </Button>

          <Button variant="outlined" onClick={() => openWin('duplicate')}
            size="small" sx={{ fontSize: '0.78rem', mr: '6px', textTransform: 'none' }}>
            Duplicate
          </Button>

          <Button variant="outlined" onClick={() => openWin('move')}
            size="small" sx={{ fontSize: '0.78rem', mr: '6px', textTransform: 'none' }}>
            Move
          </Button>
        </div>
      </CCardBody>
    </CCard>
  );
};

export default SysPrinEditButtonPanel;





// inside your window component file
import React, { useMemo } from 'react';
import { Dialog, DialogTitle, Divider } from '@mui/material';

const titleByMode = {
  changeAll: 'Change All Sys/Prins',
  edit:      'Edit Sys/Prin',
  new:       'New Sys/Prin',
  duplicate: 'Duplicate Sys/Prin',
  move:      'Move Sys/Prin',
};

export default function SysPrinInformationWindow({ open, mode = 'edit', onClose, ...rest }) {
  const title = useMemo(() => titleByMode[mode] ?? 'Sys/Prin', [mode]);

  return (
    <Dialog open={open} onClose={onClose} maxWidth="sm" fullWidth>
      <DialogTitle
        sx={{
          bgcolor: 'primary.main',       // MUI theme blue
          color: 'primary.contrastText', // white text
          py: 1,
        }}
      >
        <span style={{ fontSize: '0.9rem', fontWeight: 500 }}>{title}</span>
      </DialogTitle>
      <Divider />
      {/* ...rest of your window content... */}
    </Dialog>
  );
}



