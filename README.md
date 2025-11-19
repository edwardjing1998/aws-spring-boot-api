import React, { useEffect, useState } from 'react';
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

const EditClientEmailSetup = ({
  selectedGroupRow,
  isEditable,
  // NEW: callback to bubble changes up
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

  // Prefill from selectedGroupRow
  useEffect(() => {
    if (selectedGroupRow?.clientEmail?.length) {
      const list = selectedGroupRow.clientEmail;
      setEmailList(list);

      const formatted = list.map(
        (e) =>
          `${e.emailNameTx} <${e.emailAddressTx}>${
            e.carbonCopyFlag ? ' (CC)' : ''
          }`,
      );
      setOptions(formatted);
      setSelectedRecipients([formatted[0]]);
      updateFormFromEmail(list[0]);
    } else {
      resetForm();
    }
  }, [selectedGroupRow]);

  const updateFormFromEmail = (email) => {
    setName(email?.emailNameTx ?? '');
    setEmailAddress(email?.emailAddressTx ?? '');
    setEmailServer(emailServers[email?.mailServerId] ?? '');
    setReportId(email?.reportId ?? email?.id?.reportId ?? '');
    setIsActive(!!email?.activeFlag);
    setIsCC(!!email?.carbonCopyFlag);
  };

  const resetForm = () => {
    setName('');
    setEmailAddress('');
    setEmailServer('');
    setReportId('');
    setIsActive(false);
    setIsCC(false);
    setOptions([]);
    setSelectedRecipients([]);
    setEmailList([]);
  };

  const handleChange = (selectedOptions) => {
    const values = Array.from(selectedOptions).map((opt) => opt.value);
    setSelectedRecipients(values);

    if (values.length > 0) {
      const selected = values[0];
      const emailObj = emailList.find((email) =>
        selected.startsWith(`${email.emailNameTx} <${email.emailAddressTx}>`),
      );
      if (emailObj) updateFormFromEmail(emailObj);
    }
  };

  const handleUpdate = async () => {
    const clientId = selectedGroupRow?.client?.trim?.();
    if (!clientId) {
      setStatusMessage(
        'Missing client ID. Please select a client before updating an email.',
      );
      return;
    }
    if (!emailAddress?.trim()) {
      setStatusMessage('Email address is required.');
      return;
    }

    const mailServerId = emailServers.indexOf(emailServer);
    const payload = {
      clientId,
      reportId: reportId === '' ? 0 : Number(reportId),
      emailNameTx: name?.trim() || '',
      emailAddressTx: emailAddress.trim(),
      carbonCopyFlag: !!isCC,
      activeFlag: !!isActive,
      mailServerId: mailServerId === -1 ? 0 : mailServerId,
    };

    const selectedLabel = selectedRecipients[0] || '';
    const selectedEmailAddrMatch = selectedLabel.match(/<(.+?)>/);
    const previousEmailAddress = selectedEmailAddrMatch
      ? selectedEmailAddrMatch[1]
      : emailAddress.trim();

    try {
      const res = await fetch(
        'http://localhost:8089/client-sysprin-writer/api/email/update',
        {
          method: 'PUT',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify(payload),
        },
      );
      if (!res.ok) throw new Error(`Failed to update email (${res.status})`);
      const updated = await res.json();

      // Build next list & update local state
      const nextList = (() => {
        const idx = emailList.findIndex(
          (e) =>
            (e.emailAddressTx ?? e?.id?.emailAddressTx) ===
            previousEmailAddress,
        );
        if (idx === -1) return emailList;
        const copy = [...emailList];
        copy[idx] = updated;
        return copy;
      })();
      setEmailList(nextList);

      // Rebuild options and selection
      const respEmailAddr =
        updated.emailAddressTx ??
        updated?.id?.emailAddressTx ??
        emailAddress.trim();
      const newLabel = `${updated.emailNameTx ?? name} <${respEmailAddr}>${
        (updated.carbonCopyFlag ?? isCC) ? ' (CC)' : ''
      }`;
      const idxInOptions = emailList.findIndex(
        (e) =>
          (e.emailAddressTx ?? e?.id?.emailAddressTx) ===
          previousEmailAddress,
      );
      const newOptions = [...options];
      if (idxInOptions !== -1) newOptions[idxInOptions] = newLabel;
      setOptions(newOptions);
      setSelectedRecipients([newLabel]);

      // NEW: bubble up
      onEmailsChanged?.(clientId, nextList);

      setStatusMessage('Email updated successfully');
    } catch (err) {
      console.error('Error updating email:', err);
      setStatusMessage('Error updating email');
    }
  };

  const handleRemove = async () => {
    if (!selectedRecipients.length) return;

    const clientId = selectedGroupRow?.client?.trim?.();
    if (!clientId) {
      setStatusMessage('Missing client ID.');
      return;
    }

    const selectedLabel = selectedRecipients[0];
    const emailObj = emailList.find((email) =>
      selectedLabel.startsWith(
        `${email.emailNameTx} <${email.emailAddressTx}>`,
      ),
    );
    if (!emailObj) return;

    try {
      const res = await fetch(
        `http://localhost:8089/client-sysprin-writer/api/email/delete?clientId=${clientId}&emailAddress=${encodeURIComponent(
          emailObj.emailAddressTx,
        )}`,
        { method: 'DELETE' },
      );
      if (!res.ok) throw new Error('Failed to delete email');

      const updatedList = emailList.filter(
        (e) => e.emailAddressTx !== emailObj.emailAddressTx,
      );
      setEmailList(updatedList);

      const updatedOptions = updatedList.map(
        (email) =>
          `${email.emailNameTx} <${email.emailAddressTx}>${
            email.carbonCopyFlag ? ' (CC)' : ''
          }`,
      );
      setOptions(updatedOptions);

      if (updatedList.length > 0) {
        const nextEmail = updatedList[0];
        const nextLabel = `${nextEmail.emailNameTx} <${nextEmail.emailAddressTx}>${
          nextEmail.carbonCopyFlag ? ' (CC)' : ''
        }`;
        setSelectedRecipients([nextLabel]);
        updateFormFromEmail(nextEmail);
      } else {
        resetForm();
      }

      // NEW: bubble up
      onEmailsChanged?.(clientId, updatedList);

      setStatusMessage('Email removed successfully');
    } catch (err) {
      console.error('Error removing email:', err);
      setStatusMessage('Error removing email');
    }
  };

  const handleAdd = async () => {
    const clientId = selectedGroupRow?.client?.trim?.();
    if (!clientId) {
      setStatusMessage(
        'Missing client ID. Please select a client before adding an email.',
      );
      return;
    }
    if (!emailAddress?.trim()) {
      setStatusMessage('Email address is required.');
      return;
    }

    const mailServerId = emailServers.indexOf(emailServer);
    const payload = {
      clientId,
      reportId: reportId === '' ? 0 : Number(reportId),
      emailNameTx: name?.trim() || '',
      emailAddressTx: emailAddress.trim(),
      carbonCopyFlag: !!isCC,
      activeFlag: !!isActive,
      mailServerId: mailServerId === -1 ? 0 : mailServerId,
    };

    try {
      const res = await fetch(
        `http://localhost:8089/client-sysprin-writer/api/email/add`,
        {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify(payload),
        },
      );
      if (!res.ok) throw new Error(`Failed to add email (${res.status})`);

      const newEmail = await res.json();

      const respEmailAddr =
        newEmail.emailAddressTx ??
        newEmail?.id?.emailAddressTx ??
        emailAddress.trim();
      const formatted = `${newEmail.emailNameTx ?? name} <${respEmailAddr}>${
        (newEmail.carbonCopyFlag ?? isCC) ? ' (CC)' : ''
      }`;

      const nextList = [...emailList, newEmail];
      setEmailList(nextList);
      setOptions((prev) => [...prev, formatted]);
      setSelectedRecipients([formatted]);

      // NEW: bubble up
      onEmailsChanged?.(clientId, nextList);

      setStatusMessage('Email added successfully');
    } catch (err) {
      console.error('Error adding email:', err);
      setStatusMessage('Error adding email');
    }
  };

  return (
    <CRow
      style={{
        fontSize: '0.78rem',
        maxHeight: 420,
        overflowY: 'auto',
        marginBottom: 0,
      }}
    >
      <CCol xs={12}>
        <CCard>
          <CCardBody style={{ padding: 6 }}>
            {/* ==== MAIN 4-ROW LAYOUT ==== */}
            <div
              className="mb-3"
              style={{
                fontSize: '0.78rem',
                display: 'grid',
                rowGap: 12,
                alignItems: 'flex-start',
              }}
            >
              {/* Row 1: Email Recipients */}
              <div>
                <div style={{ marginLeft: 8 }}>
                  <div style={{ marginBottom: 12 }}>Email Recipients:</div>
                  <CFormSelect
                    multiple
                    value={selectedRecipients}
                    onChange={(e) => handleChange(e.target.selectedOptions)}
                    disabled={!isEditable}
                    style={{
                      fontSize: '0.78rem',
                      height: 220,
                      maxWidth: 400,
                      width: 400,
                      marginLeft: 0,
                    }}
                  >
                    {options.map((email, idx) => (
                      <option
                        key={idx}
                        value={email}
                        style={{ fontSize: '0.78rem' }}
                      >
                        {email}
                      </option>
                    ))}
                  </CFormSelect>
                </div>
              </div>

              {/* Row 2: Email Server + Flags */}
              <div
                style={{
                  display: 'flex',
                  alignItems: 'center',
                  gap: 24,
                  flexWrap: 'wrap',
                }}
              >
                <div style={{ minHeight: 64 }}>
                  <div style={{ marginBottom: 6 }}>Email Server:</div>
                  <CFormSelect
                    value={emailServer}
                    onChange={(e) => setEmailServer(e.target.value)}
                    disabled={!isEditable}
                    style={{ fontSize: '0.78rem', width: 260 }}
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
                    minHeight: 64,
                    display: 'flex',
                    alignItems: 'center',
                    gap: '24px',
                  }}
                >
                  <CFormCheck
                    label="Recipient Active"
                    checked={isActive}
                    onChange={(e) => setIsActive(e.target.checked)}
                    disabled={!isEditable}
                    style={{ fontSize: '0.78rem' }}
                  />
                  <CFormCheck
                    label="CC"
                    checked={isCC}
                    onChange={(e) => setIsCC(e.target.checked)}
                    disabled={!isEditable}
                    style={{ fontSize: '0.78rem' }}
                  />
                </div>
              </div>

              {/* Row 3: Name + Report ID */}
              <div
                style={{
                  display: 'flex',
                  alignItems: 'center',
                  gap: 24,
                  flexWrap: 'wrap',
                }}
              >
                <div style={{ minHeight: 64 }}>
                  <div style={{ marginBottom: 6 }}>Name:</div>
                  <CFormInput
                    value={name}
                    onChange={(e) => setName(e.target.value)}
                    disabled={!isEditable}
                    placeholder="Enter name"
                    style={{
                      fontSize: '0.78rem',
                      width: 260,
                      marginLeft: 0,
                    }}
                  />
                </div>

                <div style={{ minHeight: 64 }}>
                  <div style={{ marginBottom: 6 }}>Report ID:</div>
                  <CFormInput
                    value={reportId}
                    onChange={(e) => setReportId(e.target.value)}
                    placeholder="Enter report id"
                    disabled={!isEditable}
                    style={{ fontSize: '0.78rem', width: 140 }}
                    type="number"
                    inputMode="numeric"
                  />
                </div>
              </div>

              {/* Row 4: Email Address */}
              <div style={{ minHeight: 64 }}>
                <div style={{ marginBottom: 6 }}>Email Address:</div>
                <CFormInput
                  value={emailAddress}
                  onChange={(e) => setEmailAddress(e.target.value)}
                  placeholder="Enter email address"
                  disabled={!isEditable}
                  style={{ fontSize: '0.78rem', width: 260 }}
                  type="email"
                />
              </div>
            </div>

            {/* Status line */}
            <CRow style={{ fontSize: '0.78rem', marginBottom: 8 }}>
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
                    color: statusMessage
                      ?.toLowerCase()
                      .includes('error')
                      ? '#d32f2f'
                      : '#2e7d32',
                  }}
                  aria-live="polite"
                >
                  {statusMessage || ''}
                </div>
              </CCol>
            </CRow>

            {/* Buttons */}
            <CRow className="mt-2" style={{ marginTop: 8 }}>
              <CCol
                className="d-flex justify-content-center"
                style={{ gap: 8, paddingTop: 2, paddingBottom: 2 }}
              >
                <Button
                  variant="outlined"
                  size="small"
                  onClick={handleUpdate}
                  sx={{ fontSize: '0.78rem', textTransform: 'none' }}
                >
                  Update
                </Button>
                <Button
                  variant="outlined"
                  size="small"
                  onClick={handleAdd}
                  sx={{ fontSize: '0.78rem', textTransform: 'none' }}
                  color="primary"
                >
                  Add
                </Button>
                <Button
                  variant="outlined"
                  size="small"
                  onClick={handleRemove}
                  sx={{ fontSize: '0.78rem', textTransform: 'none' }}
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
