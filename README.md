import React, { useState, useEffect } from 'react';
import { CFormTextarea } from '@coreui/react';
import { FormControl } from '@mui/material';

const EditSysPrinNotes = ({ selectedData, onChangeGeneral, isEditable }) => {
  const MAX = { notes: 255 };
  const externalNotes = selectedData?.notes ?? '';

  // Local state so typing always feels responsive
  const [notes, setNotes] = useState(externalNotes);

  // Keep local state in sync if parent changes selectedData.notes
  useEffect(() => {
    setNotes(externalNotes);
  }, [externalNotes]);

  const notesLen = notes.length;

  const handleNotesChange = (e) => {
    const next = (e.target.value || '').slice(0, MAX.notes);

    // 1) update local UI
    setNotes(next);

    // 2) bubble to parent and update selectedData.notes
    //    Use function form so we get the latest prev value
    onChangeGeneral?.((prev) => ({
      ...(prev ?? {}),
      notes: next,
    }));
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
        value={notes}
        onChange={handleNotesChange}
        disabled={!isEditable}
        style={{
          height: '145px',
          fontSize: '0.78rem',
          fontFamily: 'Segoe UI, sans-serif',
          fontWeight: 500,
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
