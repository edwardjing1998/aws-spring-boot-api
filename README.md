import React from 'react';
import { CCard, CCardBody } from '@coreui/react';
import { Button } from '@mui/material';

const SysPrinEditButtonPanel = ({ setSysPrinInformationWindow }) => {
  return (
    <CCard style={{ height: '35px', marginBottom: '4px', marginTop: '25px' }}>
      <CCardBody
        className="d-flex align-items-center"
        style={{ padding: '0.25rem 0.5rem', height: '100%' }}
      >
        <div>
          <Button
            variant="outlined"
            onClick={() => setSysPrinInformationWindow({ open: true, mode: 'changeAll' })}
            size="small"
            sx={{ fontSize: '0.78rem', marginRight: '6px', textTransform: 'none' }}
          >
            Change All
          </Button>

          <Button
            variant="outlined"
            onClick={() => setSysPrinInformationWindow({ open: true, mode: 'edit' })}
            size="small"
            sx={{ fontSize: '0.78rem', textTransform: 'none', marginRight: '6px' }}
          >
            Edit SysPrin
          </Button>

          <Button
            variant="outlined"
            onClick={() => setSysPrinInformationWindow({ open: true, mode: 'new' })}
            size="small"
            sx={{ fontSize: '0.78rem', marginRight: '6px', textTransform: 'none' }}
          >
            New SysPrin
          </Button>

          <Button
            variant="outlined"
            onClick={() => setSysPrinInformationWindow({ open: true, mode: 'duplicate' })}
            size="small"
            sx={{ fontSize: '0.78rem', marginRight: '6px', textTransform: 'none' }}
          >
            Duplicate
          </Button>

          <Button
            variant="outlined"
            onClick={() => setSysPrinInformationWindow({ open: true, mode: 'move' })}
            size="small"
            sx={{ fontSize: '0.78rem', marginRight: '6px', textTransform: 'none' }}
          >
            Move
          </Button>
        </div>
      </CCardBody>
    </CCard>
  );
};

export default SysPrinEditButtonPanel;




import React from 'react';
import { CCard, CCardBody } from '@coreui/react';
import { Button } from '@mui/material';

const SysPrinEditButtonPanel = ({ setSysPrinInformationWindow }) => {
  return (
    <CCard style={{ height: '35px', marginBottom: '4px', marginTop: '25px' }}>
      <CCardBody
        className="d-flex align-items-center"
        style={{ padding: '0.25rem 0.5rem', height: '100%' }}
      >
        <div>
          <Button
            variant="outlined"
            onClick={() => setSysPrinInformationWindow({ open: true, mode: 'changeAll' })}
            size="small"
            sx={{ fontSize: '0.78rem', marginRight: '6px', textTransform: 'none' }}
          >
            Change All
          </Button>

          <Button
            variant="outlined"
            onClick={() => setSysPrinInformationWindow({ open: true, mode: 'edit' })}
            size="small"
            sx={{ fontSize: '0.78rem', textTransform: 'none', marginRight: '6px' }}
          >
            Edit SysPrin
          </Button>

          <Button
            variant="outlined"
            onClick={() => setSysPrinInformationWindow({ open: true, mode: 'new' })}
            size="small"
            sx={{ fontSize: '0.78rem', marginRight: '6px', textTransform: 'none' }}
          >
            New SysPrin
          </Button>

          <Button
            variant="outlined"
            onClick={() => setSysPrinInformationWindow({ open: true, mode: 'duplicate' })}
            size="small"
            sx={{ fontSize: '0.78rem', marginRight: '6px', textTransform: 'none' }}
          >
            Duplicate
          </Button>

          <Button
            variant="outlined"
            onClick={() => setSysPrinInformationWindow({ open: true, mode: 'move' })}
            size="small"
            sx={{ fontSize: '0.78rem', marginRight: '6px', textTransform: 'none' }}
          >
            Move
          </Button>
        </div>
      </CCardBody>
    </CCard>
  );
};

export default SysPrinEditButtonPanel;


