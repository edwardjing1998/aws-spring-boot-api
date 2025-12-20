import React, { useState, useEffect, useMemo, useCallback } from 'react';
import { CFormTextarea } from '@coreui/react'

import {
  FormControl,
} from '@mui/material';

const EditSysPrinNotes = ({ selectedData, isEditable, onChangeGeneral }) => {
  const MAX = { notes: 255 };
  const notesValue = selectedData?.notes ?? '';
  const notesLen = notesValue.length;

  const handleNotesChange = (e) => {
    const next = e.target.value?.slice(0, MAX.notes); // safety
    onChangeGeneral({ notes: next });
  };
  
  return (
    <FormControl fullWidth>
      <label
        htmlFor="special-notes"
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
          style={{
            fontSize: '0.72rem',
            color: notesLen >= MAX.notes ? '#d32f2f' : 'gray',
          }}
        >
          ({notesLen}/{MAX.notes})
        </span>
      </label>

      <CFormTextarea
        id="special-notes"
        aria-label="Special Processing Notes"
        value={notesValue}
        onChange={handleNotesChange}
        disabled={!isEditable}
        style={{
          height: '145px',
          fontSize: '0.78rem',
          fontFamily: 'Segoe UI, sans-serif',
          fontWeight: '500',
          overflowY: 'auto',
          resize: 'vertical',
        }}
        maxLength={MAX.notes}
        aria-describedby="notes-counter"
      />
    </FormControl>
  );
};

export default EditSysPrinNotes;
