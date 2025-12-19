import React, { useState } from 'react';
import { CCard, CCardBody, CRow, CCol } from '@coreui/react';
import TextField from '@mui/material/TextField';
import { SxProps, Theme } from '@mui/material';

// --- Interfaces ---

interface StatusLabelDef {
  code: string;
  color: string;
}

interface StatusValueDef {
  optionList: string[];
  value: string | undefined;
}

interface PreviewStatusOptionsProps {
  selectedData?: {
    statA?: string;
    statB?: string;
    statC?: string;
    statD?: string;
    statE?: string;
    statF?: string;
    statI?: string;
    statL?: string;
    statO?: string;
    statU?: string;
    statX?: string;
    statZ?: string;
    [key: string]: any;
  };
  sharedSx?: SxProps<Theme>;
  getStatusValue: (options: string[], code: string | undefined) => string;
}

const PreviewStatusOptions: React.FC<PreviewStatusOptionsProps> = ({ selectedData, getStatusValue }) => {
  const statusOptionsA = ["Destroy", "Return", "Research / Destroy", "Research / Return", "Research / Carrier Ret"];
  const statusOptionsB = ["Destroy", "Return"];
  const statusOptionsC = ["Destroy", "Return", "Research / Destroy", "Research / Return", "Research / Carrier Ret"];
  const statusOptionsD = ["Destroy", "Return", "Research / Destroy", "Research / Return", "Research / Carrier Ret"];
  const statusOptionsE = ["Destroy", "Return", "Research / Destroy", "Research / Return", "Research / Carrier Ret"];
  const statusOptionsF = ["Destroy", "Return", "Research / Destroy", "Research / Return", "Research / Carrier Ret"];
  const statusOptionsI = ["Destroy", "Return", "Research / Destroy", "Research / Return", "Research / Carrier Ret"];
  const statusOptionsL = ["Destroy", "Return"];
  const statusOptionsO = ["Destroy", "Return", "Research / Destroy", "Research / Return", "Research / Carrier Ret"];
  const statusOptionsU = ["Destroy", "Return"];
  const statusOptionsX = ["Destroy", "Return", "Research / Destroy", "Research / Return", "Research / Carrier Ret"];
  const statusOptionsZ = ["Destroy", "Return"];

  // eslint-disable-next-line @typescript-eslint/no-unused-vars
  const [isEditable] = useState(false);

  const renderStatusRow = (labels: StatusLabelDef[], values: StatusValueDef[]) => (
    <>
      <CRow style={{ height: '24px', marginBottom: '0px' }}>
        {labels.map(({ code, color }, idx) => (
          <CCol key={idx} style={{ display: 'flex', alignItems: 'center' }}>
            <div style={{
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
              marginRight: '5px'
            }}>{code}</div>
            <p style={{ margin: 0, fontSize: '0.78rem' }}>Status</p>
          </CCol>
        ))}
      </CRow>

      <CRow style={{ height: '28px', marginTop: '10px' }}>
        {values.map(({ optionList, value }, idx) => (
          <CCol key={idx} style={{ display: 'flex', alignItems: 'center' }}>
            <TextField
              placeholder="xxx"
              value={getStatusValue(optionList, value)}
              size="small"
              fullWidth
              disabled={!isEditable}
              sx={{
                '& .MuiOutlinedInput-root': {
                  height: '36px',
                  fontSize: '0.78rem'
                },
                '& .MuiOutlinedInput-input': {
                  padding: '10px 12px'
                }
              }}
            />
          </CCol>
        ))}
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
            borderRadius: '4px'
          }}
        >
          <CCardBody
            className="d-flex align-items-center"
            style={{
              padding: '0.25rem 0.5rem',
              height: '100%',
              backgroundColor: 'transparent'
            }}
          >
            <p style={{ margin: 0, fontSize: '0.78rem', fontWeight: '500' }}>General</p>
          </CCardBody>
        </CCard>

        <CCard style={{ marginBottom: '0px', marginTop: '20px', minHeight: '100px', border: 'none', boxShadow: 'none', borderBottom: '1px solid #ccc' }}>
          <CCardBody style={{
            padding: '0.25rem 0.5rem',
            backgroundColor: 'white',
            display: 'flex',
            flexDirection: 'column',
            justifyContent: 'flex-start',
            gap: '0px',
          }}>
            {renderStatusRow(
              [
                { code: 'A', color: 'red' },
                { code: 'B', color: 'brown' },
                { code: 'C', color: 'green' },
                { code: 'E', color: 'black' }
              ],
              [
                { optionList: statusOptionsA, value: selectedData?.statA },
                { optionList: statusOptionsB, value: selectedData?.statB },
                { optionList: statusOptionsC, value: selectedData?.statC },
                { optionList: statusOptionsE, value: selectedData?.statE }
              ]
            )}
          </CCardBody>
        </CCard>

        <CCard style={{ marginBottom: '0px', marginTop: '20px', minHeight: '100px', border: 'none', boxShadow: 'none', borderBottom: '1px solid #ccc', }}>
          <CCardBody style={{
            padding: '0.25rem 0.5rem',
            backgroundColor: 'white',
            display: 'flex',
            flexDirection: 'column',
            justifyContent: 'flex-start',
            gap: '0px'
          }}>
            {renderStatusRow(
              [
                { code: 'F', color: 'blue' },
                { code: 'I', color: '#f110ee' },
                { code: 'L', color: '#f17610' },
                { code: 'U', color: '#10e7f1' }
              ],
              [
                { optionList: statusOptionsF, value: selectedData?.statF },
                { optionList: statusOptionsI, value: selectedData?.statI },
                { optionList: statusOptionsL, value: selectedData?.statL },
                { optionList: statusOptionsU, value: selectedData?.statU }
              ]
            )}
          </CCardBody>
        </CCard>

        <CCard style={{ marginBottom: '0px', marginTop: '20px', minHeight: '100px', border: 'none', boxShadow: 'none', borderBottom: '1px solid #ccc' }}>
          <CCardBody style={{
            padding: '0.25rem 0.5rem',
            backgroundColor: 'white',
            display: 'flex',
            flexDirection: 'column',
            justifyContent: 'flex-start',
            gap: '0px'
          }}>
            {renderStatusRow(
              [
                { code: 'D', color: '#8410f1' },
                { code: 'O', color: 'blue' },
                { code: 'X', color: '#65167c' },
                { code: 'Z', color: '#c07bc4' }
              ],
              [
                { optionList: statusOptionsD, value: selectedData?.statD },
                { optionList: statusOptionsO, value: selectedData?.statO },
                { optionList: statusOptionsX, value: selectedData?.statX },
                { optionList: statusOptionsZ, value: selectedData?.statZ }
              ]
            )}
          </CCardBody>
        </CCard>
      </div>
    </>
  );
};

export default PreviewStatusOptions;
