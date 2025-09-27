import React, { useRef, useState, useMemo, useEffect } from 'react';
import { Dialog, DialogTitle, DialogContent, DialogActions, Button } from '@mui/material';
import * as dailyMessageService from '../../../services/AdminEditService/DailyMessageService';
import CustomSnackbar from '../../../components/CustomSnackbar';
import { CloseIcon, DailyMessage } from '../../../assets/brand/svg-constants';
import { CFormInput, CFormTextarea } from '@coreui/react';
import IconButton from '@mui/material/IconButton';
import '../../../scss/DailyMessage.scss';

const DailyMessageDialog = ({ open, onClose, action, row }) => {
  // YYYY-MM-DD helper
  const toYMD = (dLike) => {
    if (!dLike) return new Date().toISOString().slice(0, 10);
    try {
      // dLike might already be 'YYYY-MM-DD' or an ISO string
      const s = String(dLike);
      if (s.length >= 10 && s[4] === '-' && s[7] === '-') return s.slice(0, 10);
      return new Date(dLike).toISOString().slice(0, 10);
    } catch {
      return new Date().toISOString().slice(0, 10);
    }
  };

  const [selectedDate, setSelectedDate] = useState(() => new Date().toISOString().slice(0, 10));
  const [messageText, setMessageText] = useState('');
  const [originalMessageText, setOriginalMessageText] = useState('');
  const [snackbarType, setSnackbarType] = useState('none');

  const resetToToday = () => {
    setSelectedDate(new Date().toISOString().slice(0, 10));
    setMessageText('');
    setOriginalMessageText('');
  };

  const handleClose = (event, reason) => {
    if (reason === 'backdropClick' || reason === 'escapeKeyDown') return;
    setSnackbarType('none');
    resetToToday();
    onClose();
  };

  // fetch daily message by date
  const getDailyMessageDetails = async (ymd) => {
    try {
      const dateIso = `${ymd}T00:00:00`;
      const response = await dailyMessageService.fetchDailyMessageDetails(dateIso);
      const msg = response?.[0]?.messageText ?? '';
      setMessageText(msg);
      setOriginalMessageText(msg);
    } catch (error) {
      // if nothing found or error -> clear
      setMessageText('');
      setOriginalMessageText('');
    }
  };

  // When dialog opens, hydrate from clicked row (if provided), else fetch by current selectedDate
  useEffect(() => {
    if (!open) return;

    if (row && !row.isGroup) {
      const ymd = toYMD(row.messageDate || row.transDate);
      setSelectedDate(ymd);

      // If row already has messageText, use it. Otherwise fetch by date.
      if (typeof row.messageText === 'string') {
        setMessageText(row.messageText);
        setOriginalMessageText(row.messageText);
      } else {
        getDailyMessageDetails(ymd);
      }
    } else {
      // No row (e.g. "Add"), load whatever is stored for the currently selected date
      getDailyMessageDetails(selectedDate);
    }
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, [open, row, action]);

  // If user changes date manually, (re)fetch for that date
  useEffect(() => {
    if (!open) return;
    if (!row || action !== 'edit') {
      // For "add" or no specific row, changing the date should show that date's current content (if any)
      getDailyMessageDetails(selectedDate);
    }
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, [selectedDate]);

  const onSave = async () => {
    try {
      const dateIso = `${selectedDate}T00:00:00`;
      const reqData = { messageText, messageDate: dateIso };

      if (!originalMessageText) {
        await dailyMessageService.addDailyMessage(reqData);
        setSnackbarType('add');
      } else {
        await dailyMessageService.updateDailyMessage(reqData);
        setSnackbarType('update');
      }
      setOriginalMessageText(messageText);
    } catch (error) {
      // you could set a 'info'/'error' snackbar if you want
    }
  };

  const handleSnackbarCancel = () => setSnackbarType('none');

  const dialogLabel =
    action === 'edit' ? 'Edit Message' :
    action === 'add' ? 'Add Message' :
    action === 'delete' ? 'Delete Message' :
    'Daily Message';

  return (
    <>
      <Dialog open={open} onClose={handleClose} PaperProps={{ className: 'daily-message-dialog' }}>
        <DialogTitle>
          <div className="daily-message-icon"><DailyMessage /></div>
          {dialogLabel}
        </DialogTitle>
        <IconButton aria-label="close" onClick={handleClose}>
          <CloseIcon />
        </IconButton>

        <DialogContent dividers>
          {/* Optional: show the row’s original info at the top */}
          {row && !row.isGroup ? (
            <div className="row-meta" style={{ marginBottom: 8, fontSize: 12, opacity: 0.8 }}>
              <div><strong>Selected Row Date:</strong> {toYMD(row.messageDate || row.transDate)}</div>
              {row.messageText ? <div><strong>Original Text:</strong> {row.messageText}</div> : null}
            </div>
          ) : null}

          <div className="date-layout">
            <span className='date-text'>Date</span>
            <CFormInput
              type="date"
              className='date-input'
              value={selectedDate}
              onChange={e => {
                const ymd = e.target.value;
                setSelectedDate(ymd);
                // Clear while fetching
                setMessageText('');
                setOriginalMessageText('');
              }}
            />
          </div>

          <div>
            <CFormTextarea
              className='message-input'
              value={messageText}
              onChange={e => setMessageText(e.target.value)}
              maxLength={250}
            />
            {messageText.length > 0 ? (
              <span className="count-text">
                {' '}Character count - <span className='count'>{messageText.length} / 250</span>
              </span>
            ) : null}
          </div>
        </DialogContent>

        <DialogActions>
          <div className='daily-message-button-container'>
            <Button variant="outlined" size="small" onClick={handleClose}>Cancel</Button>
            <Button
              variant="contained"
              size="small"
              disabled={!messageText || messageText === originalMessageText}
              onClick={onSave}
            >
              Save
            </Button>
          </div>
        </DialogActions>
      </Dialog>

      <CustomSnackbar
        type={snackbarType}
        open={snackbarType !== 'none'}
        handleOk={handleSnackbarCancel}
        onClose={handleSnackbarCancel}
        title={(snackbarType === 'update' || snackbarType === 'add') ? 'Daily Message' : ''}
        body={
          snackbarType === 'update' ? 'You have successfully Updated a Message Text' :
          snackbarType === 'add'    ? 'You have successfully Added a Message Text'   : ''
        }
      />
    </>
  );
};

export default DailyMessageDialog;



<DailyMessageDialog
  key={`${dialogAction || 'none'}-${dialogRow?.messageDate || 'na'}-${dialogOpen}`}
  open={dialogOpen}
  onClose={handleDialogClose}
  action={dialogAction}
  row={dialogRow}
/>

