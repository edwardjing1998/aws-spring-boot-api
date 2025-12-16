const BASE_URL = 'http://localhost:8089/client-sysprin-writer/api/client';
const DELETE_BASE_URL = 'http://localhost:8089/client-sysprin-writer/api/client';

export interface ClientRowData {
  client?: string;
  name?: string;
  addr?: string;
  city?: string;
  state?: string;
  zip?: string;
  contact?: string;
  phone?: string;
  active?: boolean;
  faxNumber?: string;
  billingSp?: string;
  reportBreakFlag?: string | number;
  chLookUpType?: string | number;
  excludeFromReport?: boolean;
  positiveReports?: boolean;
  subClientInd?: boolean;
  subClientXref?: string;
  amexIssued?: boolean;
  [key: string]: any;
}

export const buildPayload = (row: ClientRowData = {}) => {
  const {
    client,
    name,
    addr,
    city,
    state,
    zip,
    contact,
    phone,
    active,
    faxNumber,
    billingSp,
    reportBreakFlag,
    chLookUpType,
    excludeFromReport,
    positiveReports,
    subClientInd,
    subClientXref,
    amexIssued,
  } = row;

  return {
    client,
    name,
    addr,
    city,
    state,
    zip,
    contact,
    phone,
    active: !!active,
    faxNumber,
    billingSp,
    reportBreakFlag:
      typeof reportBreakFlag === 'string' ? Number(reportBreakFlag) : (reportBreakFlag ?? 0),
    chLookUpType:
      typeof chLookUpType === 'string' ? Number(chLookUpType) : (chLookUpType ?? 0),
    excludeFromReport: !!excludeFromReport,
    positiveReports: !!positiveReports,
    subClientInd: !!subClientInd,
    subClientXref,
    amexIssued: !!amexIssued,
  };
};

async function fetchJson<T = any>(url: string, options: RequestInit): Promise<T | null> {
  const res = await fetch(url, options);
  if (!res.ok) {
    const text = await res.text();
    throw new Error(`${options?.method || 'GET'} ${url} failed: ${res.status} ${text}`);
  }
  // DELETE may return empty body
  const text = await res.text();
  return text ? JSON.parse(text) : null;
}

/** Create client (throws on error). Returns saved object (if backend returns JSON). */
export async function handleCreateClient(row: ClientRowData): Promise<any> {
  const payload = buildPayload(row);
  if (!payload.client) throw new Error('Client ID is required.');
  return fetchJson(`${BASE_URL}/save`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(payload),
  });
}

/** Update client (throws on error). Returns saved object. */
export async function handleUpdateClient(row: ClientRowData): Promise<any> {
  const payload = buildPayload(row);
  if (!payload.client) throw new Error('Client ID is required.');
  return fetchJson(`${BASE_URL}/update`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(payload),
  });
}

/** Delete client (throws on error). Returns null/{} depending on backend. */
export async function handleDeleteClient(clientId: string): Promise<string | null> {
  if (!clientId) throw new Error('Client ID is required to delete.');

  const res = await fetch(
    `${DELETE_BASE_URL}/delete/${encodeURIComponent(clientId)}`,
    {
      method: 'DELETE',
      headers: { accept: '*/*' }, // optional
    }
  );

  if (!res.ok) {
    let errText = '';
    try { errText = await res.text(); } catch { /* ignore */ }
    throw new Error(`DELETE ${res.url} failed: ${res.status} ${errText}`);
  }

  // Success: return plain text (or null if empty)
  try {
    const txt = await res.text();
    return txt || null; // e.g., "Client deleted successful"
  } catch {
    return null;
  }
}
