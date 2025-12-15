// clientEmailService.ts

const BASE_URL = 'http://localhost:8089/client-sysprin-writer/api/email';

// --- Types ---

export interface ClientEmail {
  clientId?: string;
  reportId?: number | string;
  emailNameTx?: string;
  emailAddressTx?: string;
  carbonCopyFlag?: boolean;
  activeFlag?: boolean;
  mailServerId?: number;
  // Handle nested ID structure sometimes returned by older APIs
  id?: {
    emailAddressTx?: string;
    reportId?: number;
  };
  [key: string]: any; // Allow other properties
}

interface ServiceResult {
  clientId: string;
  nextList: ClientEmail[];
  options: string[];
  selectedLabel: string | null;
  statusMessage: string;
}

interface UpdateClientEmailParams {
  clientId: string;
  emailServers: string[];
  emailServer: string;
  name: string;
  emailAddress: string;
  reportId: string | number;
  isActive: boolean;
  isCC: boolean;
  emailList: ClientEmail[];
  selectedRecipients: string[];
}

interface RemoveClientEmailParams {
  clientId: string;
  emailList: ClientEmail[];
  selectedRecipients: string[];
}

interface AddClientEmailParams {
  clientId: string;
  emailServers: string[];
  emailServer: string;
  name: string;
  emailAddress: string;
  reportId: string | number;
  isActive: boolean;
  isCC: boolean;
  emailList: ClientEmail[];
}

// --- Helpers ---

function buildLabel(email: ClientEmail): string {
  const name = email.emailNameTx ?? '';
  const addr = email.emailAddressTx ?? email?.id?.emailAddressTx ?? '';
  const cc = email.carbonCopyFlag ? ' (CC)' : '';
  return `${name} <${addr}>${cc}`;
}

/**
 * Update existing email recipient
 */
export async function updateClientEmail({
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
}: UpdateClientEmailParams): Promise<ServiceResult> {
  if (!clientId?.trim()) {
    throw new Error('Missing client ID. Please select a client before updating an email.');
  }
  if (!emailAddress?.trim()) {
    throw new Error('Email address is required.');
  }

  const mailServerId = emailServers.indexOf(emailServer);
  const payload = {
    clientId: clientId.trim(),
    reportId: reportId === '' ? 0 : Number(reportId),
    emailNameTx: name?.trim() || '',
    emailAddressTx: emailAddress.trim(),
    carbonCopyFlag: !!isCC,
    activeFlag: !!isActive,
    mailServerId: mailServerId === -1 ? 0 : mailServerId,
  };

  const selectedLabel = selectedRecipients?.[0] || '';
  const selectedEmailAddrMatch = selectedLabel.match(/<(.+?)>/);
  const previousEmailAddress =
    selectedEmailAddrMatch?.[1] || emailAddress.trim();

  const res = await fetch(`${BASE_URL}/update`, {
    method: 'PUT',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(payload),
  });

  if (!res.ok) {
    throw new Error(`Failed to update email (${res.status})`);
  }

  const updated: ClientEmail = await res.json();

  // Build next list
  const nextList = (() => {
    const idx = emailList.findIndex(
      (e) =>
        (e.emailAddressTx ?? e?.id?.emailAddressTx) === previousEmailAddress
    );
    if (idx === -1) return emailList;
    const copy = [...emailList];
    copy[idx] = updated;
    return copy;
  })();

  // Rebuild labels
  const options = nextList.map(buildLabel);

  // Figure out which label should be selected
  const respEmailAddr =
    updated.emailAddressTx ?? updated?.id?.emailAddressTx ?? emailAddress.trim();
  const newLabel = buildLabel({
    emailNameTx: updated.emailNameTx ?? name,
    emailAddressTx: respEmailAddr,
    carbonCopyFlag: updated.carbonCopyFlag ?? isCC,
  });

  return {
    clientId: clientId.trim(),
    nextList,
    options,
    selectedLabel: newLabel,
    statusMessage: 'Email updated successfully',
  };
}

/**
 * Remove an existing email recipient
 */
export async function removeClientEmail({
  clientId,
  emailList,
  selectedRecipients,
}: RemoveClientEmailParams): Promise<ServiceResult> {
  if (!clientId?.trim()) {
    throw new Error('Missing client ID.');
  }
  if (!selectedRecipients?.length) {
    throw new Error('No email selected to remove.');
  }

  const selectedLabel = selectedRecipients[0];
  const emailObj = emailList.find((email) =>
    selectedLabel.startsWith(
      `${email.emailNameTx} <${email.emailAddressTx}>`
    )
  );
  if (!emailObj) {
    throw new Error('Selected email not found in list.');
  }

  // Safe access for encoding
  const addrToDelete = emailObj.emailAddressTx || '';

  const res = await fetch(
    `${BASE_URL}/delete?clientId=${encodeURIComponent(
      clientId.trim()
    )}&emailAddress=${encodeURIComponent(addrToDelete)}`,
    { method: 'DELETE' }
  );

  if (!res.ok) {
    throw new Error('Failed to delete email');
  }

  const updatedList = emailList.filter(
    (e) => e.emailAddressTx !== emailObj.emailAddressTx
  );

  const options = updatedList.map(buildLabel);

  let nextSelectedLabel: string | null = null;
  if (updatedList.length > 0) {
    nextSelectedLabel = buildLabel(updatedList[0]);
  }

  return {
    clientId: clientId.trim(),
    nextList: updatedList,
    options,
    selectedLabel: nextSelectedLabel,
    statusMessage: 'Email removed successfully',
  };
}

/**
 * Add a new email recipient
 */
export async function addClientEmail({
  clientId,
  emailServers,
  emailServer,
  name,
  emailAddress,
  reportId,
  isActive,
  isCC,
  emailList,
}: AddClientEmailParams): Promise<ServiceResult> {
  if (!clientId?.trim()) {
    throw new Error(
      'Missing client ID. Please select a client before adding an email.'
    );
  }
  if (!emailAddress?.trim()) {
    throw new Error('Email address is required.');
  }

  const mailServerId = emailServers.indexOf(emailServer);
  const payload = {
    clientId: clientId.trim(),
    reportId: reportId === '' ? 0 : Number(reportId),
    emailNameTx: name?.trim() || '',
    emailAddressTx: emailAddress.trim(),
    carbonCopyFlag: !!isCC,
    activeFlag: !!isActive,
    mailServerId: mailServerId === -1 ? 0 : mailServerId,
  };

  const res = await fetch(`${BASE_URL}/add`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(payload),
  });

  if (!res.ok) {
    throw new Error(`Failed to add email (${res.status})`);
  }

  const newEmail: ClientEmail = await res.json();

  const nextList = [...emailList, newEmail];
  const options = nextList.map(buildLabel);

  const respEmailAddr =
    newEmail.emailAddressTx ??
    newEmail?.id?.emailAddressTx ??
    emailAddress.trim();
  const newLabel = buildLabel({
    emailNameTx: newEmail.emailNameTx ?? name,
    emailAddressTx: respEmailAddr,
    carbonCopyFlag: newEmail.carbonCopyFlag ?? isCC,
  });

  return {
    clientId: clientId.trim(),
    nextList,
    options,
    selectedLabel: newLabel,
    statusMessage: 'Email added successfully',
  };
}
