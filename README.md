/* Match the compact, blue-header look of ZipcodeTableDialog */

.wcd-dialog {
  /* overall dialog */
  .MuiDialog-paper {
    border-radius: 12px;
    min-width: 560px; /* tweak to your taste */
  }

  /* header */
  .MuiDialogTitle-root {
    background: #1976d2; /* same blue as ZipCode dialog */
    color: #fff;
    font-weight: 400;
    padding: 6px 40px 6px 12px; /* room for close btn */
    border-top-left-radius: 12px;
    border-top-right-radius: 12px;
    font-size: 0.9rem;
    min-height: 34px;
    line-height: 1.2;
    display: flex;
    align-items: center;
    gap: 8px;

    .wcd-dialog__icon {
      display: inline-flex;
      align-items: center;
      justify-content: center;
      width: 26px;
      height: 26px;
      border-radius: 50%;
      background: rgba(255,255,255,0.2);
      margin-right: 4px;

      svg {
        width: 18px;
        height: 18px;
      }
    }
  }

  /* top-right close button */
  .MuiIconButton-root {
    position: absolute;
    right: 6px;
    top: 6px;

    svg {
      fill: #fff; /* white icon on blue header */
    }
  }

  .MuiDialogContent-root {
    padding: 12px;
  }

  .MuiDialogActions-root {
    padding: 8px 12px;
  }
}

/* Content grid similar to Zipcode dialog */
.wcd-dialog-content {
  display: grid;
  gap: 12px;

  .wcd-row {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 12px;

    & + .wcd-row {
      margin-top: 4px;
    }
  }

  .wcd-field {
    display: flex;
    flex-direction: column;

    &.wcd-field-wide {
      grid-column: 1 / -1;
    }

    .wcd-label {
      font-size: 12px;
      color: #444;
      margin-bottom: 4px;
    }

    .wcd-input {
      /* hook for further input tweaks */
    }
  }
}

/* Buttons container (right-aligned like Zipcode) */
.wcd-button-container {
  display: flex;
  align-items: center;
  gap: 8px;
}

/* Delete confirm text block */
.wcd-dialog__delete-confirm {
  padding: 6px 2px;
}

.wcd-dialog__delete-fields {
  margin-top: 8px;
  font-size: 13px;
  opacity: 0.85;

  > div + div {
    margin-top: 2px;
  }
}






import React, { useEffect, useState } from 'react';
import { Dialog, DialogTitle, DialogContent, DialogActions, Button } from '@mui/material';
import IconButton from '@mui/material/IconButton';
import { CFormInput } from '@coreui/react';
import { CloseIcon } from '../../../assets/brand/svg-constants';
import '../../../scss/WebClientDirectory.scss'; // ← ensure this file includes styles below

/**
 * Props:
 * - open: boolean
 * - onClose: () => void
 * - mode: 'review' | 'delete' | 'addEdit'
 * - title?: string
 * - initialData?: { userId, clientId, pathTx, webReportId }
 * - onConfirmDelete?: () => void
 * - onConfirmUpdate?: (payload) => void
 * - HeaderIcon?: React component (optional) — e.g. an SVG like <Zipcode />
 */
const WebClientDirectoryDialog = ({
  open,
  onClose,
  mode = 'review',
  title,
  initialData,
  onConfirmDelete,
  onConfirmUpdate,
  HeaderIcon, // optional, if you have a directory icon available
}) => {
  const [form, setForm] = useState({
    userId: '',
    clientId: '',
    pathTx: '',
    webReportId: 0,
  });

  useEffect(() => {
    if (!open) return;
    setForm({
      userId: initialData?.userId ?? '',
      clientId: initialData?.clientId ?? '',
      pathTx: initialData?.pathTx ?? '',
      webReportId: Number(initialData?.webReportId) || 0,
    });
  }, [open, initialData]);

  const isReadOnly = mode === 'review';
  const isDelete = mode === 'delete';
  const isAddEdit = mode === 'addEdit';

  const handleUpdate = () => {
    if (!onConfirmUpdate) return;
    onConfirmUpdate({
      userId: form.userId,
      clientId: form.clientId,
      pathTx: form.pathTx,
      webReportId: Number(form.webReportId) || 0,
    });
  };

  const headerTitle =
    title ??
    (isAddEdit
      ? 'Edit Web Client Directory'
      : isDelete
      ? 'Delete Web Client Directory'
      : 'Web Client Directory Review');

  return (
    <Dialog
      open={open}
      onClose={onClose}
      PaperProps={{ className: 'wcd-dialog' }} // ← like zip-code-table-dialog
    >
      <DialogTitle>
        {/* optional icon bubble on the left, matching ZipcodeTableDialog’s layout */}
        {HeaderIcon ? <div className="wcd-dialog__icon"><HeaderIcon /></div> : null}
        {headerTitle}
      </DialogTitle>

      {/* top-right close */}
      <IconButton aria-label="close" onClick={onClose}>
        <CloseIcon />
      </IconButton>

      <DialogContent dividers>
        {isDelete ? (
          <div className="wcd-dialog__delete-confirm">
            Are you sure you want to delete this record?
            <div className="wcd-dialog__delete-fields">
              <div><strong>User Id:</strong> {initialData?.userId}</div>
              <div><strong>Client Id:</strong> {initialData?.clientId}</div>
              <div><strong>x500 Id:</strong> {initialData?.pathTx}</div>
              <div>
                <strong>Report ID:</strong>{' '}
                {String(initialData?.webReportId ?? '').padStart(5, '0')}
              </div>
            </div>
          </div>
        ) : (
          <div className="wcd-dialog-content">
            <div className="wcd-row">
              <div className="wcd-field">
                <span className="wcd-label">User Id</span>
                <CFormInput
                  value={form.userId}
                  onChange={(e) => setForm((p) => ({ ...p, userId: e.target.value }))}
                  disabled={isReadOnly}
                  className="wcd-input"
                />
              </div>
              <div className="wcd-field">
                <span className="wcd-label">Client Id</span>
                <CFormInput
                  value={form.clientId}
                  onChange={(e) => setForm((p) => ({ ...p, clientId: e.target.value }))}
                  disabled={isReadOnly}
                  className="wcd-input"
                />
              </div>
            </div>

            <div className="wcd-row">
              <div className="wcd-field wcd-field-wide">
                <span className="wcd-label">x500 Id</span>
                <CFormInput
                  value={form.pathTx}
                  onChange={(e) => setForm((p) => ({ ...p, pathTx: e.target.value }))}
                  disabled={isReadOnly}
                  className="wcd-input"
                />
              </div>
            </div>

            <div className="wcd-row">
              <div className="wcd-field">
                <span className="wcd-label">Report ID</span>
                <CFormInput
                  type="number"
                  value={form.webReportId}
                  onChange={(e) => setForm((p) => ({ ...p, webReportId: e.target.value }))}
                  disabled={isReadOnly}
                  className="wcd-input"
                />
              </div>
            </div>
          </div>
        )}
      </DialogContent>

      <DialogActions>
        <div className="wcd-button-container">
          {isDelete ? (
            <>
              <Button variant="outlined" size="small" onClick={onClose}>Cancel</Button>
              <Button variant="contained" size="small" color="error" onClick={onConfirmDelete}>
                Delete
              </Button>
            </>
          ) : isAddEdit ? (
            <>
              <Button variant="outlined" size="small" onClick={onClose}>Cancel</Button>
              <Button variant="contained" size="small" onClick={handleUpdate}>Update</Button>
            </>
          ) : (
            <Button variant="contained" size="small" onClick={onClose}>Close</Button>
          )}
        </div>
      </DialogActions>
    </Dialog>
  );
};

export default WebClientDirectoryDialog;



