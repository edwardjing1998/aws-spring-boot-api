import CIcon from '@coreui/icons-react';
import { cilFlagAlt, cilGlobeAlt, cilGlobe, cilStar } from '@coreui/icons';




const DIAL_CODES = [
  { value: '+1',  label: 'US/CA', icon: cilGlobeAlt },
  { value: '+44', label: 'UK',    icon: cilFlagAlt },
  { value: '+61', label: 'AU',    icon: cilGlobe },
  { value: '+91', label: 'IN',    icon: cilStar },
];



<TextField
  select
  value={selectedGroupRow.phoneCountryCode || '+1'}
  onChange={handleChange('phoneCountryCode')}
  size="small"
  disabled={!isEditable}
  sx={{
    ...sharedSx,
    width: 90,
    '& .MuiOutlinedInput-root': { height: 30, minHeight: 30 },
    '& .MuiInputBase-root': { height: 30 },
    '& .MuiSelect-select': {
      minHeight: 0,
      paddingTop: 4,
      paddingBottom: 4,
      lineHeight: '30px',
      fontSize: '0.78rem',
      display: 'flex',
      alignItems: 'center',
    },
  }}
  SelectProps={{
    displayEmpty: true,
    // 🔽 show icon in the closed state
    renderValue: (val) => {
      const found = DIAL_CODES.find((o) => o.value === val) || DIAL_CODES[0];
      return (
        <Box sx={{ display: 'flex', alignItems: 'center', gap: 0.5 }}>
          <CIcon icon={found.icon} size="sm" />
        </Box>
      );
    },
    MenuProps: {
      sx: {
        '& .MuiMenuItem-root': {
          fontSize: '0.72rem',
          minHeight: 30,
          lineHeight: 1.2,
        },
      },
      PaperProps: { style: { maxHeight: 280 } },
    },
  }}
>
  {DIAL_CODES.map((opt) => (
    <MenuItem key={opt.value} value={opt.value}>
      {/* 🔽 icon instead of opt.label */}
      <Box sx={{ display: 'flex', alignItems: 'center', gap: 0.5 }}>
        <CIcon icon={opt.icon} size="sm" />
      </Box>
    </MenuItem>
  ))}
</TextField>
