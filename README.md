import React from 'react';
import {
  CCard, CCardBody, CCol, CRow,
} from '@coreui/react';
import {
  Select, MenuItem, FormControl,
} from '@mui/material';

const font78 = { fontSize: '0.78rem' };

const EditStatusOptions = ({ selectedData, statusMap, setStatusMap, isEditable }) => {
  const leftStatuses  = ['a', 'b', 'c', 'd', 'e', 'f'];
  const rightStatuses = ['i', 'l', 'o', 'u', 'x', 'z'];

  const OPTIONS = {
    0: 'Return',
    1: 'Destroy',
    2: 'Research / Destroy',
    3: 'Research / Return',
    4: 'Research / Carrier Ret',
  };

  const dropdownOptionsMap = {
    a: [0, 1, 2, 3, 4],
    b: [0, 1],
    c: [0, 1, 2, 3, 4],
    d: [0, 1, 2, 3, 4],
    e: [0, 1, 2, 3, 4],
    f: [0, 1, 2, 3, 4],
    i: [0, 1, 2, 3, 4],
    l: [0, 1],
    o: [0, 1, 2, 3, 4],
    u: [0, 1],
    x: [0, 1, 2, 3, 4],
    z: [0, 1],
  };

  const handleChange = (key, value) => {
    const statusKey = `stat${key.toUpperCase()}`;
    setStatusMap((prev) => ({
      ...prev,
      [statusKey]: value,
    }));
  };

  const renderSelect = (key) => {
    const statusKey = `stat${key.toUpperCase()}`;

    return (
      <div
        key={key}
        className="d-flex align-items-center mb-1"
        style={{ gap: '0px', marginBottom: '2px' }}
      >
        <FormControl size="small" sx={{ mb: '0px', width: '100%' }}>
          <div
            style={{
              fontSize: '0.75rem',
              fontWeight: 500,
              marginBottom: '1px',
              marginLeft: '2px',
            }}
          >
            {`${key.toUpperCase()} Status`}
          </div>

          <Select
            labelId={`${statusKey}-label`}
            id={statusKey}
            value={selectedData[statusKey] ?? ''}
            onChange={(e) => handleChange(key, e.target.value)}
            sx={font78}
            disabled={!isEditable}
          >
            <MenuItem value="" sx={font78}>
              <em>None</em>
            </MenuItem>

            {dropdownOptionsMap[key].map((code) => (
              <MenuItem key={code} value={code} sx={font78}>
                {OPTIONS[code]}
              </MenuItem>
            ))}
          </Select>
        </FormControl>
      </div>
    );
  };

  const renderSelectDescription = (key) => {
    return (
      <div
        key={`desc-${key}`}
        className="d-flex align-items-center mb-1"
        style={{ gap: '6px', marginBottom: '8px', height: '32px' }}
      >
        <div style={{
          width: '15px',
          height: '15px',
          backgroundColor: 'red',
          color: 'white',
          fontWeight: 'bold',
          borderRadius: '3px',
          display: 'flex',
          alignItems: 'center',
          justifyContent: 'center',
          fontSize: '0.78rem'
        }}>
          {key.toUpperCase()}
        </div>
        <p style={{ margin: 0, fontSize: '0.78rem' }}>Status</p>
      </div>
    );
  };

  return (
    <CRow className="g-2" style={{ border: '1px solid #ccc', borderRadius: '6px' }}>
      <CCol xs={12}>
        <CCard className="mb-2">
          <CCardBody className="py-2 px-3">
            <CRow className="g-2">
              {[...leftStatuses, ...rightStatuses].map((statusKey, idx) => (
                <CCol sm={3} key={statusKey}>
                  <div className="d-flex flex-column gap-2 py-2 px-1">
                    {renderSelect(statusKey)}
                    {renderSelectDescription(statusKey)}
                  </div>
                </CCol>
              ))}
            </CRow>
          </CCardBody>
        </CCard>
      </CCol>
    </CRow>
  );
};

export default EditStatusOptions;
