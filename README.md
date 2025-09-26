import React, { useEffect, useState } from 'react';
import { Dialog, DialogTitle, DialogContent, DialogActions, Button, IconButton, Divider } from '@mui/material';
import { CloseIcon } from '../../../assets/brand/svg-constants';
import { CFormInput, CFormSelect, CFormCheck, CFormTextarea } from '@coreui/react';
import CustomSnackbar from '../../../components/CustomSnackbar';
import '../../../scss/ClientReportMapping.scss';

const ClientReportMappingDialog = ({
  open,
  onClose,
  selectedRecord,             // { clientId, reportClientId, sendToBothInd, application, description } | null
  onSuccess,                   // (type: 'add'|'update', payload) => void
  applicationOptions = [],     // [{label, value}, ...]
}) => {
  const [form, setForm] = useState({
    clientId: '',
    reportClientId: '',
    sendToBothInd: false,
    application: '',
    description: '',
  });

  const [original, setOriginal] = useState(null);
  const [snackbarType, setSnackbarType] = useState('none');

  useEffect(() => {
    if (!open) return;
    if (selectedRecord) {
      setForm({
        clientId: selectedRecord.clientId || '',
        reportClientId: selectedRecord.reportClientId || '',
        sendToBothInd: !!selectedRecord.sendToBothInd,
        application: selectedRecord.application || '',
        description: selectedRecord.description || '',
      });
      setOriginal({
        clientId: selectedRecord.clientId || '',
        reportClientId: selectedRecord.reportClientId || '',
        sendToBothInd: !!selectedRecord.sendToBothInd,
        application: selectedRecord.application || '',
        description: selectedRecord.description || '',
      });
    } else {
      setForm({
        clientId: '',
        reportClientId: '',
        sendToBothInd: false,
        application: '',
        description: '',
      });
      setOriginal(null);
    }
    setSnackbarType('none');
  }, [open, selectedRecord]);

  const handleClose = (e, reason) => {
    if (reason === 'backdropClick' || reason === 'escapeKeyDown') return;
    onClose?.();
  };

  const handleChange = (key, value) => {
    setForm((prev) => ({ ...prev, [key]: value }));
  };

  const isAdd = !original;
  const canSave = () => {
    if (!form.clientId || form.clientId.length !== 4) return false;
    if (!form.reportClientId || form.reportClientId.length !== 4) return false;
    if (!form.description.trim()) return false;
    if (!isAdd) return JSON.stringify(form) !== JSON.stringify(original);
    return true;
  };

  const handleSave = () => {
    if (form.clientId === form.reportClientId && form.sendToBothInd) {
      setSnackbarType('info');
      return;
    }
    onSuccess?.(isAdd ? 'add' : 'update', { ...form });
  };

  const handleSnackbarCancel = () => setSnackbarType('none');

  return (
    <>
      <Dialog
        open={open}
        onClose={handleClose}
        // Make the window smaller & nicely rounded
        PaperProps={{
          className: 'client-report-mapping-dialog',
          sx: {
            width: 520,                          // ← compact width
            maxWidth: 'calc(100vw - 40px)',
            borderRadius: 2,
            overflow: 'hidden',
          },
        }}
      >
        <DialogTitle sx={{ pr: 6, py: 1.25 }}>
          Client Report Mapping
          <IconButton
            aria-label="close"
            onClick={handleClose}
            size="small"
            sx={{ position: 'absolute', right: 8, top: 8 }}
          >
            <CloseIcon />
          </IconButton>
        </DialogTitle>

        <Divider />

        <DialogContent dividers sx={{ p: 2 }}>
          <div className="crm-dialog-form">
            {/* Row 1 */}
            <div className="crm-row">
              <div className="crm-field">
                <label className="crm-label">Client ID</label>
                <CFormInput
                  type="text"
                  maxLength={4}
                  value={form.clientId}
                  onChange={(e) => handleChange('clientId', e.target.value)}
                  className="crm-input"
                  disabled={!isAdd} /* lock ids during edit */
                />
              </div>
              <div className="crm-field">
                <label className="crm-label">Report Client ID</label>
                <CFormInput
                  type="text"
                  maxLength={4}
                  value={form.reportClientId}
                  onChange={(e) => handleChange('reportClientId', e.target.value)}
                  className="crm-input"
                  disabled={!isAdd}
                />
              </div>
            </div>

            {/* Row 2 */}
            <div className="crm-row">
              <div className="crm-field">
                <label className="crm-label">Application</label>
                <CFormSelect
                  className="crm-input"
                  value={form.application}
                  onChange={(e) => handleChange('application', e.target.value)}
                >
                  {applicationOptions.map((o) => (
                    <option key={o.value} value={o.value}>
                      {o.label}
                    </option>
                  ))}
                </CFormSelect>
              </div>

              <div className="crm-field crm-toggle">
                <CFormCheck
                  className="crm-checkbox"
                  checked={form.sendToBothInd}
                  onChange={(e) => handleChange('sendToBothInd', e.target.checked)}
                />
                <span className="crm-toggle-label">Send to Both</span>
              </div>
            </div>

            {/* Row 3 */}
            <div className="crm-row">
              <div className="crm-field crm-field-full">
                <label className="crm-label">Description</label>
                <CFormTextarea
                  className="crm-textarea"
                  rows={3}
                  value={form.description}
                  onChange={(e) => handleChange('description', e.target.value)}
                />
              </div>
            </div>
          </div>
        </DialogContent>

        <DialogActions sx={{ px: 2, py: 1.25 }}>
          <div className="client-report-mapping-button-container">
            <Button variant="outlined" size="small" onClick={handleClose}>
              Cancel
            </Button>
            <Button variant="contained" size="small" disabled={!canSave()} onClick={handleSave}>
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
        title={snackbarType === 'info' ? 'Confirm Action' : ''}
        body={
          snackbarType === 'info'
            ? 'Send To Both is invalid when Client ID equals Report Client ID'
            : ''
        }
      />
    </>
  );
};

export default ClientReportMappingDialog;



/* Dialog paper already sized via sx, but we keep class for any extra tweaks */
.client-report-mapping-dialog {
  /* optional overall dialog tweaks */
}

/* Form layout inside the dialog */
.crm-dialog-form {
  display: flex;
  flex-direction: column;
  gap: 12px; // vertical rhythm between form rows
}

.crm-row {
  display: grid;
  grid-template-columns: 1fr 1fr; // two columns
  gap: 12px;

  /* Full-width row variant */
  & .crm-field-full {
    grid-column: 1 / -1;
  }
}

.crm-field {
  display: flex;
  flex-direction: column;
  gap: 6px;
}

.crm-label {
  font-size: 12px;
  color: #555;
}

.crm-input {
  /* CoreUI input size adjustments */
  height: 32px;
  padding: 4px 8px;
  font-size: 13px;
}

.crm-textarea {
  min-height: 74px; /* rows=3-ish */
  font-size: 13px;
  padding: 6px 8px;
  resize: vertical;
}

/* Toggle field (Send to Both) */
.crm-toggle {
  display: flex;
  align-items: center;
  gap: 8px;

  .crm-checkbox {
    margin-top: 2px; // align with label baseline
  }
  .crm-toggle-label {
    font-size: 13px;
  }
}

/* Button row */
.client-report-mapping-button-container {
  display: flex;
  gap: 8px;
}




