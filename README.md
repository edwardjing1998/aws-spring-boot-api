import React, { useState } from 'react';
import { CCard, CCardBody, CRow, CCol } from '@coreui/react';
import TextField from '@mui/material/TextField';
import { SxProps, Theme } from '@mui/material';

// Code -> Label maps (match what SysPrinGeneral uses)
const SPECIAL_MAP: Record<string, string>    = { '0': 'None', '1': 'Destroy', '2': 'Return' };
const PIN_MAILER_MAP: Record<string, string> = { '0': 'Non',  '1': 'Destroy', '2': 'Return' };
const DESTROY_MAP: Record<string, string>    = { '0': 'None', '1': 'Destroy', '2': 'Return' };
const CUST_TYPE_MAP: Record<string, string>  = { '0': 'None', '1': 'Full Processing', '2': 'Destroy All', '3': 'Return All' };

// Unused maps in the render, but kept for compatibility if needed later
// const BAD_ADDRESS_MAP = { 'Y': 'YES', 'N': 'NO' };
// const ACCOUNT_RSCH_MAP = { 'Y': 'YES', 'N': 'NO' };

const toStr = (v: any): string => (v == null ? '' : String(v));
// eslint-disable-next-line @typescript-eslint/no-unused-vars
const asYN  = (v: any): boolean => (v === true || v === 'Y');
// eslint-disable-next-line @typescript-eslint/no-unused-vars
const as10  = (v: any): boolean => (v === true || v === '1');

const labelOf = (map: Record<string, string>, value: any): string => map[toStr(value)] ?? '';

// --- Interfaces ---

interface StatusLabelDef {
  code: string;
  color: string;
}

interface StatusValueDef {
  optionList: string[];
  value: string | undefined;
}

interface PreviewSubmitInformationAProps {
  selectedData?: {
    special?: string;
    pinMailer?: string;
    destroyStatus?: string;
    custType?: string;
    returnStatus?: string;
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
  isEditable?: boolean; // Prop exists but is shadowed by local state in the original code
  sharedSx?: SxProps<Theme>;
  getStatusValue?: (options: string[], code: string | undefined) => string;
}

const PreviewSubmitInformationA: React.FC<PreviewSubmitInformationAProps> = ({ 
  selectedData, 
  sharedSx, 
  getStatusValue 
}) => {
  // normalized display values
  const specialLabel      = labelOf(SPECIAL_MAP,    selectedData?.special);
  const pinMailerLabel    = labelOf(PIN_MAILER_MAP, selectedData?.pinMailer);
  const destroyLabel      = labelOf(DESTROY_MAP,    selectedData?.destroyStatus);
  const custTypeLabel     = labelOf(CUST_TYPE_MAP,  selectedData?.custType);
  const returnStatusText  = toStr(selectedData?.returnStatus);

  // normalized checkbox booleans (Calculated but unused in the JSX return of the provided snippet)
  /*
  const badAddressChecked   = as10(selectedData?.addrFlag);
  const accountRschChecked  = as10(selectedData?.astatRch);
  const activeChecked       = as10(selectedData?.sysPrinActive);
  const nm13Checked         = as10(selectedData?.nm13);
  const rpsChecked          = as10(selectedData?.rps);
  */

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

  // Internal state forces read-only mode for this preview component
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
  
        <CRow style={{ height: '24px', marginTop: '0px' }}>
          {values.map(({ optionList, value }, idx) => (
            <CCol key={idx} style={{ display: 'flex', alignItems: 'center' }}>
              <p style={{ margin: 0, fontSize: '0.78rem', fontWeight: '800' }}>
                {getStatusValue ? getStatusValue(optionList, value) : value}
              </p>
            </CCol>
          ))}
        </CRow>
      </>
    );


  // preview-only; if you later want them to edit, pass a setter & update correctly
  // const noop = () => {};

  return (
    <>

      <CCard
        style={{
          height: '20px',
          marginBottom: '4px',
          marginTop: '2px',
          border: 'none',
          backgroundColor: '#f3f6f8',
          boxShadow: 'none',
          borderRadius: '4px',
          color: '#0000FF'
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

      {/* Special / Pin Mailer / Destroy */}
      <CCard
        style={{
          // height: '80px',          // ⬅️ remove fixed height
          marginBottom: '4px',
          marginTop: '15px',
          border: 'none',
          boxShadow: 'none',
          borderRadius: '4px',
        }}
      >
        <CCardBody
          style={{
            padding: '0.25rem 0.5rem',
            // height: '100%',        // optional to remove
            backgroundColor: 'white',
            display: 'flex',
            flexDirection: 'column',
            justifyContent: 'flex-start',  // ⬅️ was 'space-between'
            rowGap: '2px',                 // ⬅️ small vertical gap between rows
          }}
        >
          <CRow style={{ height: '20px' }}>
            <CCol style={{ display: 'flex', alignItems: 'center' }}>
              <p style={{ margin: 0, fontSize: '0.78rem' }}>Special</p>
            </CCol>
            <CCol style={{ display: 'flex', alignItems: 'center' }}>
              <p style={{ margin: 0, fontSize: '0.78rem' }}>Pin Mailer</p>
            </CCol>
            <CCol style={{ display: 'flex', alignItems: 'center' }}>
              <p style={{ margin: 0, fontSize: '0.78rem' }}>Destroy Status</p>
            </CCol>

            <CCol style={{ display: 'flex', alignItems: 'center' }}>
              <p style={{ margin: 0, fontSize: '0.78rem' }}>Customer Type</p>
            </CCol>
            <CCol style={{ display: 'flex', alignItems: 'center' }}>
              <p style={{ margin: 0, fontSize: '0.78rem' }}>Return Status</p>
            </CCol>
          </CRow>

          <CRow style={{ height: '20px' }}>
            <CCol style={{ display: 'flex', alignItems: 'center' }}>
              <span style={{ margin: 0, fontSize: '0.78rem', fontWeight: '800' }}>{specialLabel}</span>
            </CCol>

            <CCol style={{ display: 'flex', alignItems: 'center' }}>
              <span style={{ margin: 0, fontSize: '0.78rem', fontWeight: '800' }}>{pinMailerLabel}</span>
            </CCol>

            <CCol style={{ display: 'flex', alignItems: 'center' }}>
              <span style={{ margin: 0, fontSize: '0.78rem', fontWeight: '800' }}>{destroyLabel}</span>
            </CCol>

            <CCol style={{ display: 'flex', alignItems: 'center' }}>
              <span style={{ margin: 0, fontSize: '0.78rem', fontWeight: '800' }}>{custTypeLabel}</span>
            </CCol>
            <CCol style={{ display: 'flex', alignItems: 'center' }}>
              <span style={{ margin: 0, fontSize: '0.78rem', fontWeight: '800' }}>{returnStatusText}</span>
            </CCol>

          </CRow>
        </CCardBody>
      </CCard>

      {/* Flags */}
      <CCard style={{ marginBottom: '0px', marginTop: '20px', minHeight: '100px', border: 'none', boxShadow: 'none', borderBottom: '1px solid #ccc'}}>
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
              { code: 'E', color: 'black' },
              { code: 'D', color: '#8410f1' },
              { code: 'O', color: 'blue' },
            ],
            [
              { optionList: statusOptionsA, value: selectedData?.statA },
              { optionList: statusOptionsB, value: selectedData?.statB },
              { optionList: statusOptionsC, value: selectedData?.statC },
              { optionList: statusOptionsE, value: selectedData?.statE },
              { optionList: statusOptionsD, value: selectedData?.statD },
              { optionList: statusOptionsO, value: selectedData?.statO },
            ]
          )}
        </CCardBody>
      </CCard>
      
      <CCard style={{ marginBottom: '0px', marginTop: '5px', minHeight: '50px', border: 'none', boxShadow: 'none', borderBottom: '1px solid #ccc', }}>
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
              { code: 'U', color: '#10e7f1' },
              { code: 'X', color: '#65167c' },
              { code: 'Z', color: '#c07bc4' }
            ],
            [
              { optionList: statusOptionsF, value: selectedData?.statF },
              { optionList: statusOptionsI, value: selectedData?.statI },
              { optionList: statusOptionsL, value: selectedData?.statL },
              { optionList: statusOptionsU, value: selectedData?.statU },
              { optionList: statusOptionsX, value: selectedData?.statX },
              { optionList: statusOptionsZ, value: selectedData?.statZ }
            ]
          )}
        </CCardBody>
      </CCard>
    </>
  );
};

export default PreviewSubmitInformationA;
