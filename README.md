import React from 'react'
import { CFormTextarea } from '@coreui/react'

import {
  Box,
  TextField,
  FormControl,
  Select,
  MenuItem,
  FormControlLabel,
  Checkbox,
} from '@mui/material';

const EditSysPrinNotes = ({ selectedData, setSelectedData, isEditable }) => {

  const MAX = {notes: 255};
  const notesLen = (selectedData?.notes ?? "").length;

  return (
  <FormControl fullWidth>
    <label
        htmlFor="addr-input"
        style={{
          fontSize: '0.78rem',
          marginBottom: '4px',
          display: 'flex',
          alignItems: 'center',
          gap: 6,
        }}
      >
        Notes
        <span
          id="notes-counter"
          style={{ fontSize: '0.72rem', color: notesLen >= MAX.notes ? '#d32f2f' : 'gray' }}
        >
          ({notesLen}/{MAX.notes})
        </span>
    </label>
    <CFormTextarea
      id="special-notes"
      aria-label="Special Processing Notes"
      value={selectedData.notes}
      style={{
        height: '145px',
        fontSize: '0.78rem',
        fontFamily: 'Segoe UI, sans-serif',
        fontWeight: '500',
        overflowY: 'auto',
        resize: 'vertical',
      }}
      inputProps={{ maxLength: MAX.notes, 'aria-describedby': 'notes-counter' }}
    />
  </FormControl>
  )
}

export default EditSysPrinNotes
