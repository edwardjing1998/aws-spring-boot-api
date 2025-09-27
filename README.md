import React, { useEffect, useState } from 'react';
import { Dialog, DialogTitle, DialogContent, DialogActions, Button, IconButton } from '@mui/material';
import { CloseIcon } from '../../../assets/brand/svg-constants';
import { CFormInput, CFormSelect, CFormTextarea } from '@coreui/react';
import * as emailEventIdService from '../../../services/AdminReportService/EmailEventIdService';
import CustomSnackbar from '../../../components/CustomSnackbar';
import '../../../scss/EmailEventId.scss';

/**
 * Child dialog used for Add/Edit of Event-Id mapping.
 * If `selectedRow` is null -> Add mode, otherwise Edit mode.
 */
const EmailEventIdDialog = ({
  open,
  onClose,
  selectedRow,
  applicationOptions = [],
  eventIdOptions = [],
  onSuccess,
}) => {
  const [form, setForm] = useState({
    eventId: '',
    applicationId: '',
    eventTxt: '',
  });
  const [original, setOriginal] = useState(null);
  const [snackbarType, setSnackbarType] = useState('none');

  // initialize on open
  useEffect(() => {
    if (!open) return;
    if (selectedRow) {
      // edit
      setForm({
        eventId: String(selectedRow.eventId ?? ''),
        applicationId: String(selectedRow.applicationId ?? ''),
        eventTxt: String(selectedRow.eventTxt ?? ''),
      });
      setOriginal({
        eventId: String(selectedRow.eventId ?? ''),
        applicationId: String(selectedRow.applicationId ?? ''),
        eventTxt: String(selectedRow.eventTxt ?? ''),
      });
    } else {
      // add
      setForm({ eventId: '', applicationId: '', eventTxt: '' });
      setOriginal(null);
    }
  }, [open, selectedRow]);

  const handleClose = (event, reason) => {
    if (reason === 'backdropClick' || reason === 'escapeKeyDown') return;
    onClose?.();
  };

  const changed = () => {
    if (!original) {
      // Add mode: require required fields
      return form.eventId.trim() !== '' && form.applicationId.trim() !== '' && form.eventTxt.trim() !== '';
    }
    return JSON.stringify(form) !== JSON.stringify(original);
  };

  const handleSave = async () => {
    try {
      const payload = {
        eventId: form.eventId.trim(),
        applicationId: form.applicationId.trim(),
        eventTxt: form.eventTxt.trim(),
      };

      if (selectedRow) {
        // update
        await emailEventIdService.updateEmailEventId(payload);
        onSuccess?.('update');
      } else {
        // add
        await emailEventIdService.initiateEmailEventId(payload);
        onSuccess?.('add');
      }
    } catch (err) {
      // You can show an error snackbar if you want
      setSnackbarType('info');
    }
  };

  return (
    <Dialog
      open={open}
      onClose={handleClose}
      PaperProps={{ className: 'email-event-id-edit-dialog' }} // unique class
      keepMounted
      slotProps={{
        backdrop: { style: { zIndex: 1400 } },
        paper: { style: { zIndex: 1450 } },
      }}
    >
      <DialogTitle>{selectedRow ? 'Edit Event Mapping' : 'Add Event Mapping'}</DialogTitle>
      <IconButton aria-label="close" onClick={handleClose}>
        <CloseIcon />
      </IconButton>

      <DialogContent dividers>
        <div className="email-event-id-edit-form">
          <div className="row">
            <div className="field">
              <span className="label">Event ID</span>
              <CFormSelect
                value={form.eventId}
                onChange={(e) => setForm((p) => ({ ...p, eventId: e.target.value }))}
                aria-label="Event Id"
                disabled={!!selectedRow} // lock when editing
              >
                <option value=""></option>
                {eventIdOptions.map((opt) => (
                  <option key={opt} value={opt}>
                    {opt}
                  </option>
                ))}
              </CFormSelect>
            </div>

            <div className="field">
              <span className="label">Application</span>
              <CFormSelect
                value={form.applicationId}
                onChange={(e) => setForm((p) => ({ ...p, applicationId: e.target.value }))}
                aria-label="Application"
                disabled={!!selectedRow} // lock when editing
              >
                <option value=""></option>
                {applicationOptions.map((opt) => (
                  <option key={opt} value={opt}>
                    {opt}
                  </option>
                ))}
              </CFormSelect>
            </div>
          </div>

          <div className="row">
            <div className="field field-wide">
              <span className="label">Description</span>
              <CFormTextarea
                value={form.eventTxt}
                onChange={(e) => setForm((p) => ({ ...p, eventTxt: e.target.value }))}
                rows={3}
              />
            </div>
          </div>
        </div>
      </DialogContent>

      <DialogActions>
        <div className="email-event-id-edit-buttons">
          <Button variant="outlined" size="small" onClick={handleClose}>
            Cancel
          </Button>
          <Button variant="contained" size="small" onClick={handleSave} disabled={!changed()}>
            Save
          </Button>
        </div>
      </DialogActions>

      <CustomSnackbar
        type={snackbarType}
        open={snackbarType !== 'none'}
        onClose={() => setSnackbarType('none')}
        handleOk={() => setSnackbarType('none')}
        title={snackbarType === 'info' ? 'Action Failed' : ''}
        body={snackbarType === 'info' ? 'Something went wrong. Please try again.' : ''}
      />
    </Dialog>
  );
};

export default EmailEventIdDialog;
