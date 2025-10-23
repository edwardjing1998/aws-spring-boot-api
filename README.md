import { useEffect, useState } from 'react';

import {
  CCard,
  CCardBody,
  CCol,
  CFormSelect,
  CRow,
  CButton,
} from '@coreui/react';
import {
  TextField,
  FormControl,
  Select,
  MenuItem,
} from '@mui/material';

import '../../../../scss/sys-prin-configuration/client-atm-pin-prefixes.scss';

import {
  unableToDeliver,
  forwardingAddress,
  nonUS,
  invalidState,
  isPOBox,
} from '../utils/FieldValueMapping';

const EditReMailOptions = ({ selectedData, setSelectedData, isEditable }) => {
  const getvalue = (field, fallback = '') => selectedData?.[field] ?? fallback;

  const normaliseAreaArray = (arr) =>
    arr.map((area) =>
      typeof area === 'string' ? { area, sysPrin: selectedData.sysPrin ?? '' } : area
    );

  const getAreaNames = () =>
    getvalue('invalidDelivAreas', []).map((a) => (typeof a === 'string' ? a : a.area));

  const [selectedInvalidAreas, setSelectedInvalidAreas] = useState([]);

  useEffect(() => {
    setSelectedInvalidAreas(getAreaNames());
  }, [selectedData?.invalidDelivAreas]);

//  const sysPrinItems = getvalue('sysPrins', []);
 // const firstSysPrin = sysPrinItems?.[0];

 // console.log("firstSysPrin " + firstSysPrin);
 // console.log("selectedData.sysPrins " + selectedData.sysPrins);
 // console.log("getvalue('sysPrins') " + getvalue('sysPrins'));
 // console.log("getvalue('holdDays') " + getvalue('holdDays'));
 // console.log("selectedData.name == " + selectedData.name);
 // console.log("selectedData.tempAway == " + selectedData.tempAway);
 // console.log("getvalue('tempAway') " + getvalue('tempAway'));


  useEffect(() => {
    if (!selectedData) {
      console.log('selectedData is null/undefined');
      return;
    }
  
    // Interactive (expandable) object in DevTools:
    console.log('selectedData (object):', selectedData);
  
    // Pretty, immutable snapshot:
    try {
      console.log('selectedData (JSON):\n' + JSON.stringify(selectedData, null, 2));
    } catch (e) {
      console.warn('JSON stringify failed (maybe circular refs). Logging raw object instead.', e);
    }
  
    // A couple of handy specifics:
    console.log('sysPrins:', selectedData?.sysPrins);
    console.log('sysPrins[0]:', Array.isArray(selectedData?.sysPrins) ? selectedData.sysPrins[0] : undefined);
  }, [selectedData]);
  

  
  
  const updateField = (field) => (value) =>
    setSelectedData((prev) => ({ ...prev, [field]: value }));

  const handleInvalidAreasChange = (e) => {
    const areas = Array.from(e.target.selectedOptions, (o) => o.value);
    setSelectedInvalidAreas(areas);
    updateField('invalidDelivAreas')(normaliseAreaArray(areas));
  };

  const font78 = { fontSize: '0.78rem' };

  const leftLabel = {
    fontSize: '0.75rem',
    fontWeight: 500,
    minWidth: '160px',   // tweak width as you like
    marginLeft: '2px',
  };
  

  return (
    <CRow className="d-flex justify-content-between align-items-stretch">
      {/* ----------- LEFT column ----------- */}
      <CCol xs={6} className="d-flex justify-content-start">
        <CCard className="mb-0 w-100 d-flex">
          <CCardBody className="d-flex flex-column" style={{ gap: '25px' }}>
            {/* Days to Hold — inline label on the left */}
            <div style={{ display: 'flex', alignItems: 'center', gap: '12px' }}>
              <div style={leftLabel}>Days to Hold</div>
              <TextField
                variant="outlined"
                fullWidth
                size="small"
                value={getvalue('holdDays')}
                onChange={(e) => updateField('holdDays')(e.target.value)}
                InputProps={{ sx: font78 }}
                disabled={!isEditable}
              />
            </div>

            {/* Days to Hold Temp Aways — inline */}
            <div style={{ display: 'flex', alignItems: 'center', gap: '12px' }}>
              <div style={leftLabel}>Days to Hold Temp Aways</div>
              <TextField
                variant="outlined"
                fullWidth
                size="small"
                value={getvalue('tempAway')}
                onChange={(e) => updateField('tempAway')(e.target.value)}
                InputProps={{ sx: font78 }}
                disabled={!isEditable}
              />
            </div>

            {/* Re-Mail Attempts — inline */}
            <div style={{ display: 'flex', alignItems: 'center', gap: '12px' }}>
              <div style={leftLabel}>Re-Mail Attempts</div>
              <TextField
                variant="outlined"
                fullWidth
                size="small"
                value={getvalue('tempAwayAtts')}
                onChange={(e) => updateField('tempAwayAtts')(e.target.value)}
                InputProps={{ sx: font78 }}
                disabled={!isEditable}
              />
            </div>

            {/* Unable to Deliver — inline */}
            <div style={{ display: 'flex', alignItems: 'center', gap: '12px' }}>
              <div style={{ ...leftLabel, whiteSpace: 'nowrap' }}>Unable to Deliver</div>
              <FormControl fullWidth size="small" disabled={!isEditable} sx={{ flex: 1 }}>
                <Select
                  labelId="undeliverable-label"
                  id="undeliverable"
                  value={getvalue('undeliverable')}
                  onChange={(e) => updateField('undeliverable')(e.target.value)}
                  sx={font78}
                >
                  {unableToDeliver.map((opt) => (
                    <MenuItem key={opt.code} value={opt.code} sx={font78}>
                      {opt.label}
                    </MenuItem>
                  ))}
                </Select>
              </FormControl>
            </div>

            {/* Forwarding Address — inline */}
            <div style={{ display: 'flex', alignItems: 'center', gap: '12px' }}>
              <div style={{ ...leftLabel, whiteSpace: 'nowrap' }}>Forwarding Address</div>
              <FormControl fullWidth size="small" disabled={!isEditable} sx={{ flex: 1 }}>
                <Select
                  labelId="forwarding-label"
                  id="forwarding"
                  value={getvalue('forwardingAddress')}
                  onChange={(e) => updateField('forwardingAddress')(e.target.value)}
                  sx={font78}
                >
                  {forwardingAddress.map((opt) => (
                    <MenuItem key={opt.code} value={opt.code} sx={font78}>
                      {opt.label}
                    </MenuItem>
                  ))}
                </Select>
              </FormControl>
            </div>
          </CCardBody>
        </CCard>
      </CCol>

      {/* ----------- RIGHT column ----------- */}
      <CCol xs={6} className="d-flex justify-content-end">
        <CCard className="mb-0 w-100" style={{ height: 'auto' }}>
          <CCardBody className="d-flex flex-column gap-3">
            <h4 className="text-body-secondary small mb-1" style={font78}>
              <code>Do Not Deliver to …</code>
            </h4>

            <CFormSelect
              size="lg"
              multiple
              aria-label="Do Not Deliver States"
              value={selectedInvalidAreas}
              onChange={handleInvalidAreasChange}
              style={{ height: '90px', overflowY: 'auto', fontSize: '0.78rem' }}
            >
              {getvalue('invalidDelivAreas', []).map((area, idx) => {
                const name = typeof area === 'string' ? area : area.area;
                return (
                  <option key={idx} value={name}>
                    {name}
                  </option>
                );
              })}
            </CFormSelect>

            {/* Non-US — inline */}
              <div style={{ display: 'flex', alignItems: 'center', gap: '12px' }}>
                <div style={{ ...leftLabel, whiteSpace: 'nowrap' }}>Non-US</div>
                <FormControl fullWidth size="small" disabled={!isEditable} sx={{ flex: 1 }}>
                  <Select
                    labelId="nonus-label"
                    id="nonus"
                    value={getvalue('nonUS')}
                    onChange={(e) => updateField('nonUS')(e.target.value)}
                    sx={font78}
                  >
                    {nonUS.map((opt) => (
                      <MenuItem key={opt.code} value={opt.code} sx={font78}>
                        {opt.label}
                      </MenuItem>
                    ))}
                  </Select>
                </FormControl>
              </div>

              {/* Address is P.O. Box — inline */}
              <div style={{ display: 'flex', alignItems: 'center', gap: '12px' }}>
                <div style={{ ...leftLabel, whiteSpace: 'nowrap' }}>Address is P.O. Box</div>
                <FormControl fullWidth size="small" disabled={!isEditable} sx={{ flex: 1 }}>
                  <Select
                    labelId="pobox-label"
                    id="pobox"
                    value={getvalue('poBox')}
                    onChange={(e) => updateField('poBox')(e.target.value)}
                    sx={font78}
                  >
                    {isPOBox.map((opt) => (
                      <MenuItem key={opt.code} value={opt.code} sx={font78}>
                        {opt.label}
                      </MenuItem>
                    ))}
                  </Select>
                </FormControl>
              </div>

              {/* Invalid State — inline */}
              <div style={{ display: 'flex', alignItems: 'center', gap: '12px' }}>
                <div style={{ ...leftLabel, whiteSpace: 'nowrap' }}>Invalid State</div>
                <FormControl fullWidth size="small" disabled={!isEditable} sx={{ flex: 1 }}>
                  <Select
                    labelId="badstate-label"
                    id="badstate"
                    value={getvalue('badState')}
                    onChange={(e) => updateField('badState')(e.target.value)}
                    sx={font78}
                  >
                    {invalidState.map((opt) => (
                      <MenuItem key={opt.code} value={opt.code} sx={font78}>
                        {opt.label}
                      </MenuItem>
                    ))}
                  </Select>
                </FormControl>
              </div>
          </CCardBody>
        </CCard>
      </CCol>
    </CRow>
  );
};

export default EditReMailOptions;
