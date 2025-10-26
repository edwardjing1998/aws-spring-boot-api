import React from 'react';
import { CCard, CCardBody, CRow, CCol } from '@coreui/react';
import TextField from '@mui/material/TextField';
import FormControlLabel from '@mui/material/FormControlLabel';
import Checkbox from '@mui/material/Checkbox'


const PreviewGeneralInformation = ({ selectedData, isEditable, sharedSx, getStatusValue }) => {
  const specialStatus = ["Destroy", "Return"];
  const pinMailerStatus = ["Destroy", "Return"];
  const destroyStatus = ["Destroy", "Return"];
  const customerTypeStatus = ["Full Processing", "Destroy All", "Return All"];


  const handleCheckboxChange = (field) => (event) => {
    const checked = event.target.checked;
   // setSelectedData((prev) => ({ ...prev, [field]: checked }));
};
  

  return (
    <>
      {/* General Section */}
      <CCard
          style={{
            height: '35px',
            marginBottom: '4px',
            marginTop: '2px',
            border: 'none',              // remove border
            backgroundColor: '#f3f6f8',  // light light green
            boxShadow: 'none',           // remove any shadow
            borderRadius: '4px'          // optional: rounded corners
          }}
        >
          <CCardBody
            className="d-flex align-items-center"
            style={{
              padding: '0.25rem 0.5rem',
              height: '100%',
              backgroundColor: 'transparent' // inherit from parent
            }}
          >
            <p
              style={{
                margin: 0,
                fontSize: '0.78rem',
                fontWeight: '500'
              }}
            >
              General
            </p>
          </CCardBody>
        </CCard>

      {/* Special, Pin Mailer, Destroy Status Section */}
      <CCard style={{ height: '80px', marginBottom: '4px', marginTop: '15px', border: 'none', boxShadow: 'none', borderRadius: '4px'  }}>
        <CCardBody
          style={{
            padding: '0.25rem 0.5rem',
            height: '100%',
            backgroundColor: 'white',
            display: 'flex',
            flexDirection: 'column',
            justifyContent: 'space-between',
          }}
        >
          {/* Row 1 */}
          <CRow style={{ height: '25px' }}>
            <CCol style={{ display: 'flex', alignItems: 'center' }}>
              <p style={{ margin: 0, fontSize: '0.78rem' }}>Special</p>
            </CCol>
            <CCol style={{ display: 'flex', alignItems: 'center' }}>
              <p style={{ margin: 0, fontSize: '0.78rem' }}>Pin Mailer</p>
            </CCol>
            <CCol style={{ display: 'flex', alignItems: 'center' }}>
              <p style={{ margin: 0, fontSize: '0.78rem' }}>Destroy Status</p>
            </CCol>
          </CRow>

          {/* Row 2 */}
          <CRow style={{ height: '25px' }}>
            <CCol style={{ display: 'flex', alignItems: 'center' }}>

              <TextField
                  placeholder="xxx"
                  value={getStatusValue(specialStatus, selectedData?.special)}
                  size="small"
                  fullWidth
                  disabled={!isEditable}
                  sx={{
                    ...sharedSx,
                    marginTop: '-12px',
                    '& .MuiInputBase-root': {
                      minHeight: '25px',     // Reduce input height
                      fontSize: '0.78rem',    // Smaller font
                      padding: '0px 0px',    // Tighter padding
                    },
                    '& input': {
                      padding: '0px 0px',    // Reduce inner input padding
                    }
                  }}
                />

            </CCol>
            <CCol style={{ display: 'flex', alignItems: 'center' }}>
              <TextField
                placeholder="xxx"
                value={getStatusValue(pinMailerStatus, selectedData?.pinMailer)}
                size="small"
                fullWidth
                disabled={!isEditable}
                sx={{
                  ...sharedSx,
                  marginTop: '-12px',
                  '& .MuiInputBase-root': {
                    minHeight: '25px',     // Reduce input height
                    fontSize: '0.78rem',    // Smaller font
                    padding: '0px 0px',    // Tighter padding
                  },
                  '& input': {
                    padding: '0px 0px',    // Reduce inner input padding
                  }
                }}
              />
            </CCol>
            <CCol style={{ display: 'flex', alignItems: 'center' }}>
              <TextField
                placeholder="xxx"
                value={getStatusValue(destroyStatus, selectedData?.destroyStatus)}
                size="small"
                fullWidth
                disabled={!isEditable}
                sx={{
                  ...sharedSx,
                  marginTop: '-12px',
                  '& .MuiInputBase-root': {
                    minHeight: '25px',     // Reduce input height
                    fontSize: '0.78rem',    // Smaller font
                    padding: '0px 0px',    // Tighter padding
                  },
                  '& input': {
                    padding: '0px 0px',    // Reduce inner input padding
                  }
                }}
              />
            </CCol>
          </CRow>
        </CCardBody>
      </CCard>
      <CCard
        style={{
          marginTop: '30px',
          height: '80px',
          marginBottom: '4px',
          border: 'none',  
          boxShadow: 'none',
          borderTop: '1px solid #ccc',  
          borderRadius: '4px' 
        }}
      >            
      <CCardBody
                style={{
                padding: '0.25rem 0.5rem',
                height: '100%',
                backgroundColor: 'white',
                display: 'flex',
                flexDirection: 'column',
                justifyContent: 'space-between',
                }}
            >
                {/* Row 1 */}
                <CRow style={{ height: '25px' }}>
                <CCol style={{ display: 'flex', alignItems: 'center' }}>
                    <p style={{ margin: 0, fontSize: '0.78rem' }}>Customer Type</p>
                </CCol>
                <CCol style={{ display: 'flex', alignItems: 'center' }}>
                    <p style={{ margin: 0, fontSize: '0.78rem' }}>Return Status</p>
                </CCol>
                <CCol style={{ display: 'flex', alignItems: 'center' }}>
                    
                </CCol>
                </CRow>

                {/* Row 2 */}
                <CRow style={{ height: '25px' }}>
                <CCol style={{ display: 'flex', alignItems: 'center'}}>
                    
                    <TextField
                    placeholder="0"
                    value={getStatusValue(customerTypeStatus, selectedData?.custType)}
                    size="small"
                    fullWidth
                    disabled={!isEditable}
                    sx={{
                      ...sharedSx,
                      marginTop: '-12px',
                      '& .MuiInputBase-root': {
                        minHeight: '25px',     // Reduce input height
                        fontSize: '0.78rem',    // Smaller font
                        padding: '0px 0px',    // Tighter padding
                      },
                      '& input': {
                        padding: '0px 0px',    // Reduce inner input padding
                      }
                    }}
                    />

                </CCol>
                <CCol style={{ display: 'flex', alignItems: 'center' }}>
                    
                <TextField
                    placeholder="xxx"
                    value={selectedData?.returnStatus || ''}
                    size="small"
                    fullWidth
                    disabled={!isEditable}
                    sx={{
                      ...sharedSx,
                      marginTop: '-12px',
                      '& .MuiInputBase-root': {
                        minHeight: '25px',     // Reduce input height
                        fontSize: '0.78rem',    // Smaller font
                        padding: '0px 0px',    // Tighter padding
                      },
                      '& input': {
                        padding: '0px 0px',    // Reduce inner input padding
                      }
                    }}
                    />

                </CCol>
                <CCol style={{ display: 'flex', alignItems: 'center' }}>
                </CCol>
                </CRow>
    </CCardBody>
    </CCard>

    <CCard style={{ marginTop: '30px', marginBottom: '10px', height: '100px' }}>
  <CCardBody
    style={{
      padding: '0.8rem',
      backgroundColor: 'white',
      display: 'flex',
      flexDirection: 'column',
      rowGap: '0px',
      height: '100%', // ensure it stretches to match the card height
    }}
  >
    {/* Row 1 */}
    <CRow style={{ height: '30px' }}>
      <CCol style={{ display: 'flex', alignItems: 'center', justifyContent: 'flex-start', paddingLeft: '4px', flex: '0 0 41%', maxWidth: '41%' }}>
        <FormControlLabel
          control={<Checkbox size="small" checked={selectedData.addrFlag === 'Y'} onChange={handleCheckboxChange('active')} disabled={!isEditable} />}
          label="Bad Address"
          sx={{
            backgroundColor: 'white',
            pl: 1,
            m: 0,
            '& .MuiFormControlLabel-label': { fontSize: '0.78rem', color: 'black' },
            '& .Mui-disabled + .MuiFormControlLabel-label': { color: 'black' },
            paddingLeft: '0px',
            paddingRight: '0px',
          }}
        />
      </CCol>
      <CCol style={{ display: 'flex', alignItems: 'center', justifyContent: 'flex-start', paddingLeft: '4px', flex: '0 0 59%', maxWidth: '59%' }}>
        <FormControlLabel
          control={<Checkbox size="small" checked={selectedData.astatRch === '1'} onChange={handleCheckboxChange('active')} disabled={!isEditable} />}
          label="Account Research"
          sx={{
            backgroundColor: 'white',
            pl: 1,
            m: 0,
            paddingLeft: '2px',
            paddingRight: '0px',
            '& .MuiFormControlLabel-label': { fontSize: '0.78rem', color: 'black' },
            '& .Mui-disabled + .MuiFormControlLabel-label': { color: 'black' },
          }}
        />
      </CCol>
    </CRow>

    {/* Row 2 */}
    <CRow style={{ height: '30px' }}>
      <CCol style={{ display: 'flex', alignItems: 'center', justifyContent: 'flex-start', paddingLeft: '4px', flex: '0 0 42%', maxWidth: '42%' }}>
        <FormControlLabel
          control={<Checkbox size="small" checked={selectedData.active} onChange={handleCheckboxChange('active')} disabled={!isEditable} />}
          label="Active"
          sx={{
            backgroundColor: 'white',
            pl: 1,
            m: 0,
            paddingLeft: '0px',
            paddingRight: '0px',
            '& .MuiFormControlLabel-label': { fontSize: '0.78rem', color: 'black' },
            '& .Mui-disabled + .MuiFormControlLabel-label': { color: 'black' },
          }}
        />
      </CCol>
      <CCol style={{ display: 'flex', alignItems: 'center', justifyContent: 'flex-start', paddingLeft: '4px', flex: '0 0 58%', maxWidth: '58%' }}>
        <FormControlLabel
          control={<Checkbox size="small" checked={selectedData.nm13 === '1'} disabled={!isEditable} />}
          label="Non-Mon 13 on Destroy"
          sx={{
            backgroundColor: 'white',
            pl: 1,
            m: 0,
            paddingLeft: '0px',
            paddingRight: '0px',
            '& .MuiFormControlLabel-label': { fontSize: '0.78rem', color: 'black' },
            '& .Mui-disabled + .MuiFormControlLabel-label': { color: 'black' },
          }}
        />
      </CCol>
    </CRow>
  </CCardBody>
</CCard>

    </>
  );
};

export default PreviewGeneralInformation;
