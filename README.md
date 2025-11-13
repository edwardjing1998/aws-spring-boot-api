import { FormControl, InputLabel, Select, MenuItem } from '@mui/material';

<CCol xs={3}>
  <FormControl fullWidth size="small" disabled={!isEditable}>
    <InputLabel id="destroy-status-label" sx={{ fontSize: '0.78rem' }}>
      Destroy Status
    </InputLabel>
    <Select
      labelId="destroy-status-label"
      id="destroy-status"
      label="Destroy Status"
      value={destroyStatus}
      onChange={handleChange('destroyStatus')}
      sx={{ fontSize: '0.78rem' }}
    >
      <MenuItem value="0" sx={{ fontSize: '0.78rem' }}>None</MenuItem>
      <MenuItem value="1" sx={{ fontSize: '0.78rem' }}>Destroy</MenuItem>
      <MenuItem value="2" sx={{ fontSize: '0.78rem' }}>Return</MenuItem>
    </Select>
  </FormControl>
</CCol>
