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
  onDeleteConfirm,             // () => void (only used in 'delete' mode)
  applicationOptions = [],     // [{label, value}, ...]
  mode = 'addEdit',            // 'addEdit' | 'delete'
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
      const f = {
        clientId: selectedRecord.clientId || '',
        reportClientId: selectedRecord.reportClientId || '',
        sendToBothInd: !!selectedRecord.sendToBothInd,
        application: selectedRecord.application || '',
        description: selectedRecord.description || '',
      };
      setForm(f);
      setOriginal(f);
    } else {
      setForm({ clientId: '', reportClientId: '', sendToBothInd: false, application: '', description: '' });
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

  // Title varies by mode
  const title =
    mode === 'delete' ? 'Confirm Delete'
    : isAdd ? 'Add Client Report Mapping'
    : 'Edit Client Report Mapping';

  return (
    <>
      <Dialog
        open={open}
        onClose={handleClose}
        PaperProps={{
          className: 'client-report-mapping-dialog',
          sx: {
            width: 520,
            height: mode === 'delete' ? 260 : '50vh',
            maxHeight: '50vh',
            maxWidth: 'calc(100vw - 40px)',
            borderRadius: 2,
            overflow: 'hidden',
          },
        }}
      >
        <DialogTitle sx={{ pr: 6, py: 1.25, bgcolor: '#0d6efd', color:'white' }}>
          {title}
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

        {mode === 'delete' ? (
          <DialogContent dividers sx={{ p: 2 }}>
            <div style={{ lineHeight: 1.6 }}>
              Are you sure you want to delete this mapping?
              <br />
              <strong>Client:</strong> {form.clientId || '(n/a)'} &nbsp; | &nbsp;
              <strong>Report Client:</strong> {form.reportClientId || '(n/a)'}
              <br />
              <strong>Description:</strong> {form.description || '(n/a)'}
            </div>
          </DialogContent>
        ) : (
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
        )}

        <DialogActions sx={{ px: 2, py: 1.25 }}>
          <div className="client-report-mapping-button-container">
            <Button variant="outlined" size="small" onClick={handleClose}>
              Cancel
            </Button>
            {mode === 'delete' ? (
              <Button
                variant="contained"
                size="small"
                color="error"
                onClick={onDeleteConfirm}
              >
                Delete
              </Button>
            ) : (
              <Button variant="contained" size="small" disabled={!canSave()} onClick={handleSave}>
                Save
              </Button>
            )}
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
  onDeleteConfirm,             // () => void
  mode = 'addEdit',            // 'addEdit' | 'delete'
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
      const next = {
        clientId: selectedRecord.clientId || '',
        reportClientId: selectedRecord.reportClientId || '',
        sendToBothInd: !!selectedRecord.sendToBothInd,
        application: selectedRecord.application || '',
        description: selectedRecord.description || '',
      };
      setForm(next);
      setOriginal(next);
    } else {
      const empty = {
        clientId: '',
        reportClientId: '',
        sendToBothInd: false,
        application: '',
        description: '',
      };
      setForm(empty);
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
    if (mode === 'delete') return true;
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

  const handleDelete = () => {
    onDeleteConfirm?.();
  };

  const handleSnackbarCancel = () => setSnackbarType('none');

  const isDeleteMode = mode === 'delete';

  return (
    <>
      <Dialog
        open={open}
        onClose={handleClose}
        PaperProps={{
          className: 'client-report-mapping-dialog',
          sx: {
            width: 520,
            height: '50vh',
            maxHeight: '50vh',
            maxWidth: 'calc(100vw - 40px)',
            borderRadius: 2,
            overflow: 'hidden',
          },
        }}
      >
        <DialogTitle sx={{ pr: 6, py: 1.25, bgcolor: '#0d6efd', color: 'white' }}>
          {isDeleteMode ? 'Delete Client Report Mapping' : 'Client Report Mapping'}
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
          {isDeleteMode ? (
            <div className="crm-dialog-form">
              <p style={{ margin: 0, fontWeight: 600 }}>Are you sure you want to delete this mapping?</p>
              <div style={{ marginTop: 10, fontSize: 13, lineHeight: 1.5 }}>
                <div><strong>Client ID:</strong> {form.clientId}</div>
                <div><strong>Report Client ID:</strong> {form.reportClientId}</div>
                <div><strong>Send To Both:</strong> {form.sendToBothInd ? 'Yes' : 'No'}</div>
                <div><strong>Application:</strong> {form.application || '(blank)'}</div>
                <div><strong>Description:</strong> {form.description}</div>
              </div>
            </div>
          ) : (
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
                    disabled={!isAdd}
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
          )}
        </DialogContent>

        <DialogActions sx={{ px: 2, py: 1.25 }}>
          <div className="client-report-mapping-button-container">
            <Button variant="outlined" size="small" onClick={handleClose}>
              Cancel
            </Button>
            {isDeleteMode ? (
              <Button variant="contained" size="small" color="error" onClick={handleDelete}>
                Delete
              </Button>
            ) : (
              <Button variant="contained" size="small" disabled={!canSave()} onClick={handleSave}>
                Save
              </Button>
            )}
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



