import React, { useEffect, useState, useRef, ChangeEvent } from 'react';
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
} from './ClientEmail.service';

// --- Types & Interfaces ---
import {
  ClientEmail,
  EditClientEmailSetupProps,
  ServiceResult
} from './ClientEmail.type';

const EditClientEmailSetup: React.FC<EditClientEmailSetupProps> = ({
  selectedGroupRow,
  isEditable,
  onEmailsChanged,
}) => {
  // State Types
  const [emailList, setEmailList] = useState<ClientEmail[]>([]);
  const [options, setOptions] = useState<string[]>([]);
  const [selectedRecipients, setSelectedRecipients] = useState<string[]>([]);

  // ✅ NEW: track the “current/active” selected recipient so Remove won't delete the wrong record
  const [activeRecipient, setActiveRecipient] = useState<string>('');

  const [name, setName] = useState<string>('');
  const [emailAddress, setEmailAddress] = useState<string>('');
  const [emailServer, setEmailServer] = useState<string>('');
  const [reportId, setReportId] = useState<string>('');

  const [isActive, setIsActive] = useState<boolean>(false);
  const [isCC, setIsCC] = useState<boolean>(false);
  const [statusMessage, setStatusMessage] = useState<string>('');

  // Pagination for Email List
  const [emailPage, setEmailPage] = useState<number>(0);
  const EMAIL_PAGE_SIZE = 10;

  const emailServers: string[] = [
    'Omaha-SMTP Server (uschaappsmtp.1dc.com)',
    'Cha-SMTP Server (uschaappsmtp.1dc.com)',
  ];

  // Tracks which clientId we last loaded to control when to auto-prefill
  const clientIdRef = useRef<string>('');

  // ✅ helper: one single format used everywhere (options + matching)
  const formatOption = (e: ClientEmail) =>
    `${e.emailNameTx} <${e.emailAddressTx}>${e.carbonCopyFlag ? ' (CC)' : ''}`;

  // ---------- Helpers to reset form ----------

  const resetFormFields = () => {
    setName('');
    setEmailAddress('');
    setEmailServer('');
    setReportId('');
    setIsActive(false);
    setIsCC(false);
  };

  const resetForm = () => {
    resetFormFields();
    setOptions([]);
    setSelectedRecipients([]);
    setActiveRecipient(''); // ✅ clear active
    setEmailList([]);
    setEmailPage(0); // Reset page on form reset
  };

  const updateFormFromEmail = (email: ClientEmail) => {
    setName(email?.emailNameTx ?? '');
    setEmailAddress(email?.emailAddressTx ?? '');
    // Ensure mailServerId maps correctly to our array
    setEmailServer(emailServers[email?.mailServerId] ?? '');

    // Handle nested reportId structure
    const rId = email?.reportId ?? email?.id?.reportId ?? '';
    setReportId(String(rId));

    setIsActive(!!email?.activeFlag);
    setIsCC(!!email?.carbonCopyFlag);
  };

  // Prefill from selectedGroupRow, but ONLY auto-fill on client change
  useEffect(() => {
    const clientId = selectedGroupRow?.client?.trim?.() || '';
    const list = selectedGroupRow?.clientEmail ?? [];

    // Keep local list in sync
    setEmailList(list);

    // If no emails for this client -> clear everything
    if (!list.length) {
      resetForm();
      clientIdRef.current = clientId;
      return;
    }

    // Build the dropdown options
    const formatted = list.map(formatOption);
    setOptions(formatted);

    // Only auto-select & prefill when we SWITCH to a different client
    if (clientIdRef.current !== clientId) {
      clientIdRef.current = clientId;
      setEmailPage(0); // Reset page on client switch

      if (formatted.length > 0) {
        setSelectedRecipients([formatted[0]]);
        setActiveRecipient(formatted[0]); // ✅ set active
        updateFormFromEmail(list[0]);
      }
    }
  }, [selectedGroupRow]);

  // Pagination Logic
  const pageCount = Math.max(1, Math.ceil(options.length / EMAIL_PAGE_SIZE));

  // Ensure current page is valid if options shrink
  useEffect(() => {
    if (emailPage > 0 && emailPage >= pageCount) {
      setEmailPage(Math.max(0, pageCount - 1));
    }
  }, [options.length, emailPage, pageCount]);

  const pageOptions = options.slice(
    emailPage * EMAIL_PAGE_SIZE,
    (emailPage + 1) * EMAIL_PAGE_SIZE
  );

  // ✅ Handle Select Change with Pagination support (fixed)
  const handleChange = (e: ChangeEvent<HTMLSelectElement>) => {
    const newlySelected = Array.from(e.target.selectedOptions, (o) => o.value);
    const currentVisibleOptions = new Set(pageOptions);

    // Merge: Keep selections NOT on current page + add new ones from current page
    const newTotalSelection = [
      ...selectedRecipients.filter((val) => !currentVisibleOptions.has(val)),
      ...newlySelected,
    ];

    setSelectedRecipients(newTotalSelection);

    // ✅ Determine which one is “active” (what user just interacted with on THIS page)
    // For typical single-click, newlySelected[0] is the clicked one.
    // If user multi-selects, we take the last one in newlySelected as the most recent.
    const picked =
      (newlySelected.length ? newlySelected[newlySelected.length - 1] : '') ||
      (newTotalSelection.length ? newTotalSelection[newTotalSelection.length - 1] : '');

    if (picked) {
      setActiveRecipient(picked); // ✅ store active selection
      const emailObj = emailList.find((email) => formatOption(email) === picked);
      if (emailObj) updateFormFromEmail(emailObj);
    }
  };

  // -------------------------
  // Service-based handlers
  // -------------------------

  const handleUpdateEmail = async () => {
    try {
      const clientId = selectedGroupRow?.client?.trim?.() || '';

      const result: ServiceResult = await updateClientEmail({
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
      setActiveRecipient(''); // ✅ clear active
      resetFormFields();

      // bubble up to parent
      onEmailsChanged?.(result.clientId, result.nextList);

      setStatusMessage(result.statusMessage);
    } catch (err: any) {
      console.error('Error updating email:', err);
      setStatusMessage(err.message || 'Error updating email');
    }
  };

  const handleRemoveEmail = async () => {
    try {
      const clientId = selectedGroupRow?.client?.trim?.() || '';

      // ✅ FIX:
      // Always remove the “active” one (last user-clicked), not selectedRecipients[0]
      const target = activeRecipient || selectedRecipients[selectedRecipients.length - 1] || '';
      if (!target) {
        setStatusMessage('Please select an email recipient to remove.');
        return;
      }

      const result: ServiceResult = await removeClientEmail({
        clientId,
        emailList,
        // pass only the target so service can't accidentally delete a different one
        selectedRecipients: [target],
      });

      setEmailList(result.nextList);
      setOptions(result.options);

      // ✅ always clear selection + fields after remove
      setSelectedRecipients([]);
      setActiveRecipient(''); // ✅ clear active
      resetFormFields();

      // bubble up
      onEmailsChanged?.(result.clientId, result.nextList);

      setStatusMessage(result.statusMessage);
    } catch (err: any) {
      console.error('Error removing email:', err);
      setStatusMessage(err.message || 'Error removing email');
    }
  };

  const handleAddEmail = async () => {
    try {
      const clientId = selectedGroupRow?.client?.trim?.() || '';

      const result: ServiceResult = await addClientEmail({
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
      setActiveRecipient(''); // ✅ clear active
      resetFormFields();

      // bubble up
      onEmailsChanged?.(result.clientId, result.nextList);

      setStatusMessage(result.statusMessage);
    } catch (err: any) {
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
                  onChange={(e: ChangeEvent<HTMLInputElement>) => setName(e.target.value)}
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
                  onChange={(e: ChangeEvent<HTMLInputElement>) => setEmailAddress(e.target.value)}
                  placeholder="Enter email address"
                  disabled={!isEditable}
                  style={{ fontSize: '0.73rem', width: 260 }}
                  type="email"
                />
              </div>

              <div style={{ gridRow: '1 / span 5' }}>
                <div style={{ marginLeft: 8 }}>
                  <div style={{ marginBottom: 12 }}>
                    Email Recipients: {selectedGroupRow?.clientEmailTotal ?? 0} items
                  </div>
                  <CFormSelect
                    multiple
                    value={selectedRecipients}
                    onChange={handleChange}
                    disabled={!isEditable}
                    // @ts-ignore: CoreUI size prop type issue
                    size={10 as any}
                    style={{
                      fontSize: '0.73rem',
                      height: 250,
                      maxWidth: 400,
                      width: 400,
                      marginLeft: 0,
                    }}
                  >
                    {pageOptions.map((email) => (
                      <option key={email} value={email} style={{ fontSize: '0.73rem' }}>
                        {email}
                      </option>
                    ))}
                  </CFormSelect>

                  {/* Pagination Controls */}
                  <div style={{ display: 'flex', justifyContent: 'space-between', alignItems: 'center', marginTop: '4px', maxWidth: '400px' }}>
                    <Button
                      variant="outlined"
                      size="small"
                      color="inherit"
                      disabled={emailPage === 0}
                      onClick={() => setEmailPage(p => Math.max(0, p - 1))}
                      sx={{ fontSize: '0.7rem', padding: '2px 6px', minWidth: 'auto', textTransform: 'none', color: '#666', borderColor: '#ccc' }}
                    >
                      Prev
                    </Button>
                    <span style={{ fontSize: '0.75rem', color: '#666' }}>
                      Page {emailPage + 1} of {pageCount}
                    </span>
                    <Button
                      variant="outlined"
                      size="small"
                      color="inherit"
                      disabled={emailPage >= pageCount - 1}
                      onClick={() => setEmailPage(p => Math.min(pageCount - 1, p + 1))}
                      sx={{ fontSize: '0.7rem', padding: '2px 6px', minWidth: 'auto', textTransform: 'none', color: '#666', borderColor: '#ccc' }}
                    >
                      Next
                    </Button>
                  </div>
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
                  onChange={(e: ChangeEvent<HTMLSelectElement>) => setEmailServer(e.target.value)}
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
                  borderBottom: '1px dotted #ccc',
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
                    onChange={(e: ChangeEvent<HTMLInputElement>) => setIsActive(e.target.checked)}
                    disabled={!isEditable}
                    style={{ fontSize: '0.73rem' }}
                  />

                  <CFormCheck
                    label="CC"
                    checked={isCC}
                    onChange={(e: ChangeEvent<HTMLInputElement>) => setIsCC(e.target.checked)}
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
                    <span style={{ fontSize: '0.73rem', marginRight: 2 }}>
                      Report ID
                    </span>

                    <CFormInput
                      value={reportId}
                      onChange={(e: ChangeEvent<HTMLInputElement>) => setReportId(e.target.value)}
                      placeholder="input"
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
