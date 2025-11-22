import React, { useEffect, useState, useRef } from 'react';
import {
  CCard,
  CCardBody,
  CRow,
  CCol,
  CFormSelect,
  CFormInput,
  CFormCheck,
} from '@coreui/react';
import { Button } from '@mui/material';

import {
  updateClientEmail,
  removeClientEmail,
  addClientEmail,
} from './utils/ClientEmailService'; // 👈 adjust path as needed

const EditClientEmailSetup = ({
  selectedGroupRow,
  isEditable,
  // callback to bubble changes up
  onEmailsChanged,
}) => {
  const [emailList, setEmailList] = useState([]);
  const [options, setOptions] = useState([]);
  const [selectedRecipients, setSelectedRecipients] = useState([]);

  const [name, setName] = useState('');
  const [emailAddress, setEmailAddress] = useState('');
  const [emailServer, setEmailServer] = useState('');
  const [reportId, setReportId] = useState('');

  const [isActive, setIsActive] = useState(false);
  const [isCC, setIsCC] = useState(false);
  const [statusMessage, setStatusMessage] = useState('');

  const emailServers = [
    'Omaha-SMTP Server (uschaappsmtp.1dc.com)',
    'Cha-SMTP Server (uschaappsmtp.1dc.com)',
  ];

  // Tracks which clientId we last loaded to control when to auto-prefill
  const clientIdRef = useRef('');

  // ---------- helpers to reset form ----------

  // reset only the input fields (what you asked for)
  const resetFormFields = () => {
    setName('');
    setEmailAddress('');
    setEmailServer('');
    setReportId('');
    setIsActive(false);
    setIsCC(false);
  };

  // reset everything including lists & options (used when no emails)
  const resetForm = () => {
    resetFormFields();
    setOptions([]);
    setSelectedRecipients([]);
    setEmailList([]);
  };

  const updateFormFromEmail = (email) => {
    setName(email?.emailNameTx ?? '');
    setEmailAddress(email?.emailAddressTx ?? '');
    setEmailServer(emailServers[email?.mailServerId] ?? '');
    setReportId(email?.reportId ?? email?.id?.reportId ?? '');
    setIsActive(!!email?.activeFlag);
    setIsCC(!!email?.carbonCopyFlag);
  };

  // Prefill from selectedGroupRow, but ONLY auto-fill on client change
  useEffect(() => {
    const clientId = selectedGroupRow?.client?.trim?.() || '';
    const list = selectedGroupRow?.clientEmail ?? [];

    // keep local list in sync
    setEmailList(list);

    // If no emails for this client -> clear everything
    if (!list.length) {
      resetForm();
      clientIdRef.current = clientId;
      return;
    }

    // Build the dropdown options
    const formatted = list.map(
      (e) =>
        `${e.emailNameTx} <${e.emailAddressTx}>${
          e.carbonCopyFlag ? ' (CC)' : ''
        }`,
    );
    setOptions(formatted);

    // Only auto-select & prefill when we SWITCH to a different client
    if (clientIdRef.current !== clientId) {
      clientIdRef.current = clientId;

      setSelectedRecipients([formatted[0]]);
      updateFormFromEmail(list[0]);
    }
  }, [selectedGroupRow]);

  const handleChange = (selectedOptions) => {
    const values = Array.from(selectedOptions).map((opt) => opt.value);
    setSelectedRecipients(values);

    if (values.length > 0) {
      const selected = values[0];
      const emailObj = emailList.find((email) =>
        selected.startsWith(
          `${email.emailNameTx} <${email.emailAddressTx}>`,
        ),
      );
      if (emailObj) updateFormFromEmail(emailObj);
    }
  };

  // -------------------------
  // Service-based handlers
  // -------------------------

  const handleUpdateEmail = async () => {
    try {
      const clientId = selectedGroupRow?.client?.trim?.() || '';

      const result = await updateClientEmail({
        clientId,
        emailServers,
        emailServer,
        name,
        emailAddress,
        reportId,
        isActive,
        isCC,
        emailList,
        selectedRecipients,
      });

      setEmailList(result.nextList);
      setOptions(result.options);

      // ✅ clear selection + form fields after update
      setSelectedRecipients([]);
      resetFormFields();

      // bubble up to parent
      onEmailsChanged?.(result.clientId, result.nextList);

      setStatusMessage(result.statusMessage);
    } catch (err) {
      console.error('Error updating email:', err);
      setStatusMessage(err.message || 'Error updating email');
    }
  };

  const handleRemoveEmail = async () => {
    try {
      const clientId = selectedGroupRow?.client?.trim?.() || '';

      const result = await removeClientEmail({
        clientId,
        emailList,
        selectedRecipients,
      });

      setEmailList(result.nextList);
      setOptions(result.options);

      // ✅ always clear selection + fields after remove
      setSelectedRecipients([]);
      resetFormFields();

      // bubble up
      onEmailsChanged?.(result.clientId, result.nextList);

      setStatusMessage(result.statusMessage);
    } catch (err) {
      console.error('Error removing email:', err);
      setStatusMessage(err.message || 'Error removing email');
    }
  };

  const handleAddEmail = async () => {
    try {
      const clientId = selectedGroupRow?.client?.trim?.() || '';

      const result = await addClientEmail({
        clientId,
        emailServers,
        emailServer,
        name,
        emailAddress,
        reportId,
        isActive,
        isCC,
        emailList,
      });

      setEmailList(result.nextList);
      setOptions(result.options);

      // ✅ clear selection + form fields after add
      setSelectedRecipients([]);
      resetFormFields();

      // bubble up
      onEmailsChanged?.(result.clientId, result.nextList);

      setStatusMessage(result.statusMessage);
    } catch (err) {
      console.error('Error adding email:', err);
      setStatusMessage(err.message || 'Error adding email');
    }
  };

  return (
    <CRow
      style={{ fontSize: '0.73rem', height: 450, overflowY: 'auto', marginBottom: 0 }}
    >
      <CCol xs={12}>
        <CCard style={{ minHeight: 420, width: '100%' }}>
          <CCardBody style={{ padding: 6 }}>
            <div
              className="mb-3"
              style={{
                fontSize: '0.73rem',
                display: 'grid',
                gridTemplateColumns: 'auto auto',
                columnGap: '16px',
                alignItems: 'start',
              }}
            >
              <div style={{ minHeight: 70, borderBottom: '1px dotted #ccc' }}>
                <div style={{ marginBottom: 6 }}>Name:</div>
                <CFormInput
                  value={name}
                  onChange={(e) => setName(e.target.value)}
                  disabled={!isEditable}
                  placeholder="Enter name"
                  style={{ fontSize: '0.73rem', width: 260, marginLeft: 0 }}
                />
              </div>

              <div
                style={{
                  minHeight: 70,
                  borderBottom: '1px dotted #ccc',
                  marginTop: 10,
                }}
              >
                <div style={{ marginBottom: 6 }}>Email Address:</div>
                <CFormInput
                  value={emailAddress}
                  onChange={(e) => setEmailAddress(e.target.value)}
                  placeholder="Enter email address"
                  disabled={!isEditable}
                  style={{ fontSize: '0.73rem', width: 260 }}
                  type="email"
                />
              </div>

              <div style={{ gridRow: '1 / span 5' }}>
                <div style={{ marginLeft: 8 }}>
                  <div style={{ marginBottom: 12 }}>Email Recipients:</div>
                  <CFormSelect
                    multiple
                    value={selectedRecipients}
                    onChange={(e) => handleChange(e.target.selectedOptions)}
                    disabled={!isEditable}
                    style={{
                      fontSize: '0.73rem',
                      height: 220,
                      maxWidth: 400,
                      width: 400,
                      marginLeft: 0,
                    }}
                  >
                    {options.map((email, idx) => (
                      <option key={idx} value={email} style={{ fontSize: '0.73rem' }}>
                        {email}
                      </option>
                    ))}
                  </CFormSelect>
                </div>
              </div>

              <div
                style={{
                  minHeight: 70,
                  marginTop: 10,
                  borderBottom: '1px dotted #ccc',
                }}
              >
                <div style={{ marginBottom: 6 }}>Email Server:</div>
                <CFormSelect
                  value={emailServer}
                  onChange={(e) => setEmailServer(e.target.value)}
                  disabled={!isEditable}
                  style={{ fontSize: '0.73rem', width: 260 }}
                >
                  <option value="">Select Email Server</option>
                  {emailServers.map((srv, idx) => (
                    <option key={idx} value={srv}>
                      {srv}
                    </option>
                  ))}
                </CFormSelect>
              </div>

              <div
                style={{
                  minHeight: 55,
                  display: 'flex',
                  flexDirection: 'column',
                  justifyContent: 'flex-end',
                  borderBottom: '0px dotted #ccc',
                  marginTop: 0,
                }}
              >
                <div
                  style={{
                    display: 'flex',
                    alignItems: 'center',
                    gap: '24px',
                  }}
                >
                  <CFormCheck
                    label="Active"
                    checked={isActive}
                    onChange={(e) => setIsActive(e.target.checked)}
                    disabled={!isEditable}
                    style={{ fontSize: '0.73rem' }}
                  />

                  <CFormCheck
                    label="CC"
                    checked={isCC}
                    onChange={(e) => setIsCC(e.target.checked)}
                    disabled={!isEditable}
                    style={{ fontSize: '0.73rem' }}
                  />

                  <div
                    style={{
                      display: 'flex',
                      alignItems: 'center',
                      gap: 10,
                      marginTop: -10,
                    }}
                  >
                    <span
                      style={{
                        fontSize: '0.73rem',
                        marginRight: 2,
                      }}
                    >
                      Report ID
                    </span>

                    <CFormInput
                      value={reportId}
                      onChange={(e) => setReportId(e.target.value)}
                      placeholder="Enter report id"
                      disabled={!isEditable}
                      size="sm"
                      type="number"
                      inputMode="numeric"
                      style={{
                        fontSize: '0.73rem',
                        width: 60,
                        height: '24px',
                        padding: '0 4px',
                        lineHeight: '24px',
                        margin: 0,
                      }}
                    />
                  </div>
                </div>
              </div>
            </div>

            <CRow style={{ fontSize: '0.73rem', marginBottom: 8 }}>
              <CCol>
                <div
                  style={{
                    height: 22,
                    lineHeight: '22px',
                    overflow: 'hidden',
                    whiteSpace: 'nowrap',
                    textOverflow: 'ellipsis',
                    fontWeight: 'bold',
                    visibility: statusMessage ? 'visible' : 'hidden',
                    color: statusMessage?.toLowerCase().includes('error')
                      ? '#d32f2f'
                      : '#2e7d32',
                  }}
                  aria-live="polite"
                >
                  {statusMessage || ''}
                </div>
              </CCol>
            </CRow>

            <CRow className="mt-2" style={{ marginTop: 8 }}>
              <CCol
                className="d-flex"
                style={{
                  gap: 8,
                  paddingTop: 2,
                  paddingBottom: 2,
                  justifyContent: 'center',
                }}
              >
                <Button
                  variant="outlined"
                  size="small"
                  onClick={handleUpdateEmail}
                  sx={{ fontSize: '0.73rem', textTransform: 'none' }}
                >
                  Update
                </Button>
                <Button
                  variant="outlined"
                  size="small"
                  onClick={handleAddEmail}
                  sx={{ fontSize: '0.73rem', textTransform: 'none' }}
                  color="primary"
                >
                  Add
                </Button>
                <Button
                  variant="outlined"
                  size="small"
                  onClick={handleRemoveEmail}
                  sx={{ fontSize: '0.73rem', textTransform: 'none' }}
                  color="error"
                >
                  Remove
                </Button>
              </CCol>
            </CRow>
          </CCardBody>
        </CCard>
      </CCol>
    </CRow>
  );
};

export default EditClientEmailSetup;
