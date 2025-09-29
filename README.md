// inside DailyMessageDialog.js

// 1) On open: keep as-is (prefill from row or fetch once)
useEffect(() => {
  if (!open) return;

  if (row && !row.isGroup) {
    const ymd = toYMD(row.messageDate || row.transDate);
    setSelectedDate(ymd);

    if (typeof row.messageText === 'string') {
      setMessageText(row.messageText);
      setOriginalMessageText(row.messageText);
    } else {
      getDailyMessageDetails(ymd); // only if row has no text
    }
  } else {
    // "New" or no row: fetch current date
    getDailyMessageDetails(selectedDate);
  }
  // eslint-disable-next-line react-hooks/exhaustive-deps
}, [open, row, action]);

// 2) On selectedDate change: fetch ONLY for "add" (or no row).
useEffect(() => {
  if (!open) return;

  // if adding (or no row provided), changing date should refetch;
  // but for review/delete/edit with a provided row, don't override the row's text.
  const shouldFetch = action === 'add' || !row;
  if (shouldFetch) {
    getDailyMessageDetails(selectedDate);
  }
  // eslint-disable-next-line react-hooks/exhaustive-deps
}, [selectedDate]);

// 3) Keep your onChange for the date input:
//    only clear text while fetching in editable modes (not review/delete)
<CFormInput
  type="date"
  className='date-input'
  value={selectedDate}
  onChange={(e) => {
    const ymd = e.target.value;
    setSelectedDate(ymd);
    if (!(action === 'review' || action === 'delete')) {
      setMessageText('');
      setOriginalMessageText('');
    }
  }}
  disabled={action === 'delete'}
/>
