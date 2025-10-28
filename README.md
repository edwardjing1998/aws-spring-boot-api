// PreviewStatusOptions.jsx
import React, { useState } from 'react';
import { CCard, CCardBody, CRow, CCol } from '@coreui/react';
import { Select, MenuItem } from '@mui/material';

const PreviewStatusOptions = ({ selectedData, sharedSx, getStatusValue, onChangeGeneral }) => {
  // Editable now
  const [isEditable] = useState(true);

  // ✅ Code → Label map (same meanings as your previous getStatusValue)
  const CODE_TO_LABEL = {
    '0': 'Return',
    '1': 'Destroy',
    '2': 'Research / Destroy',
    '3': 'Research / Return',
    '4': 'Research / Carrier Ret',
  };

  // ✅ Allowed codes per status (kept identical to your previous groupings)
  const ALLOWED_CODES = {
    A: ['0', '1', '2', '3', '4'],
    B: ['0', '1'],
    C: ['0', '1', '2', '3', '4'],
    D: ['0', '1', '2', '3', '4'],
    E: ['0', '1', '2', '3', '4'],
    F: ['0', '1', '2', '3', '4'],
    I: ['0', '1', '2', '3', '4'],
    L: ['0', '1'],
    O: ['0', '1', '2', '3', '4'],
    U: ['0', '1'],
    X: ['0', '1', '2', '3', '4'],
    Z: ['0', '1'],
  };

  // Colors (unchanged visuals)
  const BADGES = {
    A: 'red',
    B: 'brown',
    C: 'green',
    D: '#8410f1',
    E: 'black',
    F: 'blue',
    I: '#f110ee',
    L: '#f17610',
    O: 'blue',
    U: '#10e7f1',
    X: '#65167c',
    Z: '#c07bc4',
  };

  const font78 = { fontSize: '0.78rem' };

  // Helper to bubble single field changes
  const pushPatch = (keyUpper, code) => {
    if (typeof onChangeGeneral === 'function') {
      onChangeGeneral({
        client: selectedData?.client,
        sysPrin: selectedData?.sysPrin,
        [`stat${keyUpper}`]: code ?? '',
      });
    }
  };

  const renderStatusRow = (labels, values) => (
    <>
      {/* Badge row */}
      <CRow style={{ height: '24px', marginBottom: '0px' }}>
        {labels.map(({ code, color }, idx) => (
          <CCol key={idx} style={{ display: 'flex', alignItems: 'center' }}>
            <div
              style={{
                width: '15px',
                height: '15px',
                backgroundColor: color,
                color: 'white',
                fontWeight: 'bold',
                borderRadius: '3px',
                display: 'flex',
                alignItems: 'center',
                justifyContent: 'center',
                fontSize: '0.78rem',
                marginRight: '5px',
              }}
            >
              {code}
            </div>
            <p style={{ margin: 0, fontSize: '0.78rem' }}>Status</p>
          </CCol>
        ))}
      </CRow>

      {/* Editable Select row */}
      <CRow style={{ height: '28px', marginTop: '10px' }}>
        {values.map(({ statusKey, value }, idx) => {
          const keyUpper = statusKey.replace('stat', '').toUpperCase(); // e.g., statA -> A
          const allowed = ALLOWED_CODES[keyUpper] || [];
          const currentCode = (value ?? '') === '' ? '' : String(value);

          return (
            <CCol key={idx} style={{ display: 'flex', alignItems: 'center' }}>
              <Select
                displayEmpty
                size="small"
                fullWidth
                disabled={!isEditable}
                value={currentCode}
                onChange={(e) => pushPatch(keyUpper, e.target.value)}
                sx={{
                  '& .MuiOutlinedInput-root': {
                    height: '36px',
                    fontSize: '0.78rem',
                  },
                  '& .MuiOutlinedInput-input': {
                    padding: '6px 10px',
                  },
                  ...(sharedSx || {}),
                }}
                renderValue={(val) =>
                  val === '' ? <em style={{ color: 'rgba(0,0,0,0.6)' }}>None</em> : CODE_TO_LABEL[val] ?? val
                }
              >
                <MenuItem value="">
                  <em>None</em>
                </MenuItem>
                {allowed.map((code) => (
                  <MenuItem key={code} value={code} sx={font78}>
                    {CODE_TO_LABEL[code]}
                  </MenuItem>
                ))}
              </Select>
            </CCol>
          );
        })}
      </CRow>
    </>
  );

  return (
    <>
      <div style={{ overflow: 'visible' }}>
        <CCard
          style={{
            height: '35px',
            marginBottom: '4px',
            marginTop: '2px',
            border: 'none',
            backgroundColor: '#f3f6f8',
            boxShadow: 'none',
            borderRadius: '4px',
          }}
        >
          <CCardBody
            className="d-flex align-items-center"
            style={{ padding: '0.25rem 0.5rem', height: '100%', backgroundColor: 'transparent' }}
          >
            <p style={{ margin: 0, fontSize: '0.78rem', fontWeight: '500' }}>General</p>
          </CCardBody>
        </CCard>

        {/* Group 1: A, B, C, E */}
        <CCard
          style={{
            marginBottom: '0px',
            marginTop: '20px',
            minHeight: '100px',
            border: 'none',
            boxShadow: 'none',
            borderBottom: '1px solid #ccc',
          }}
        >
          <CCardBody
            style={{
              padding: '0.25rem 0.5rem',
              backgroundColor: 'white',
              display: 'flex',
              flexDirection: 'column',
              justifyContent: 'flex-start',
              gap: '0px',
            }}
          >
            {renderStatusRow(
              [
                { code: 'A', color: BADGES.A },
                { code: 'B', color: BADGES.B },
                { code: 'C', color: BADGES.C },
                { code: 'E', color: BADGES.E },
              ],
              [
                { statusKey: 'statA', value: selectedData?.statA },
                { statusKey: 'statB', value: selectedData?.statB },
                { statusKey: 'statC', value: selectedData?.statC },
                { statusKey: 'statE', value: selectedData?.statE },
              ]
            )}
          </CCardBody>
        </CCard>

        {/* Group 2: F, I, L, U */}
        <CCard
          style={{
            marginBottom: '0px',
            marginTop: '20px',
            minHeight: '100px',
            border: 'none',
            boxShadow: 'none',
            borderBottom: '1px solid #ccc',
          }}
        >
          <CCardBody
            style={{
              padding: '0.25rem 0.5rem',
              backgroundColor: 'white',
              display: 'flex',
              flexDirection: 'column',
              justifyContent: 'flex-start',
              gap: '0px',
            }}
          >
            {renderStatusRow(
              [
                { code: 'F', color: BADGES.F },
                { code: 'I', color: BADGES.I },
                { code: 'L', color: BADGES.L },
                { code: 'U', color: BADGES.U },
              ],
              [
                { statusKey: 'statF', value: selectedData?.statF },
                { statusKey: 'statI', value: selectedData?.statI },
                { statusKey: 'statL', value: selectedData?.statL },
                { statusKey: 'statU', value: selectedData?.statU },
              ]
            )}
          </CCardBody>
        </CCard>

        {/* Group 3: D, O, X, Z */}
        <CCard
          style={{
            marginBottom: '0px',
            marginTop: '20px',
            minHeight: '100px',
            border: 'none',
            boxShadow: 'none',
            borderBottom: '1px solid #ccc',
          }}
        >
          <CCardBody
            style={{
              padding: '0.25rem 0.5rem',
              backgroundColor: 'white',
              display: 'flex',
              flexDirection: 'column',
              justifyContent: 'flex-start',
              gap: '0px',
            }}
          >
            {renderStatusRow(
              [
                { code: 'D', color: BADGES.D },
                { code: 'O', color: BADGES.O },
                { code: 'X', color: BADGES.X },
                { code: 'Z', color: BADGES.Z },
              ],
              [
                { statusKey: 'statD', value: selectedData?.statD },
                { statusKey: 'statO', value: selectedData?.statO },
                { statusKey: 'statX', value: selectedData?.statX },
                { statusKey: 'statZ', value: selectedData?.statZ },
              ]
            )}
          </CCardBody>
        </CCard>
      </div>
    </>
  );
};

export default PreviewStatusOptions;
