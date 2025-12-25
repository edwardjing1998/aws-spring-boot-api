ClientReportWindow.service.ts


import environment from '../../Environments/Environment';

const CLIENT_SYSPRIN_WRITER_API_BASE = environment.clientWriterBaseUrl;

export interface ClientReportPayload {
  clientId: string;
  reportId: number | null;
  receiveFlag: boolean;
  outputTypeCd: number;
  fileTypeCd: number;
  emailFlag: number;
  emailBodyTx: string;
  reportPasswordTx: string;
  fileExt: string;
}

async function requestJson(url: string, options: RequestInit) {
  const res = await fetch(url, {
    headers: {
      accept: '*/*',
      ...(options.body ? { 'Content-Type': 'application/json' } : {}),
      ...(options.headers || {}),
    },
    ...options,
  });

  if (!res.ok) {
    const text = await res.text().catch(() => '');
    throw new Error(text || `${options.method || 'GET'} ${url} failed: ${res.status}`);
  }

  // backend may return json or empty body
  const text = await res.text().catch(() => '');
  if (!text) return null;

  try {
    return JSON.parse(text);
  } catch {
    return text; // sometimes backend returns plain text
  }
}

export async function createClientReport(payload: ClientReportPayload) {
  const url = `${CLIENT_SYSPRIN_WRITER_API_BASE}/client/reportOptions/Initiate`;
  return requestJson(url, { method: 'POST', body: JSON.stringify(payload) });
}

export async function updateClientReport(payload: ClientReportPayload) {
  const url = `${CLIENT_SYSPRIN_WRITER_API_BASE}/client/reportOptions/update`;
  return requestJson(url, { method: 'POST', body: JSON.stringify(payload) });
}

export async function deleteClientReport(clientId: string, reportId: number) {
  const url = `${CLIENT_SYSPRIN_WRITER_API_BASE}/client/reportOptions/delete/${encodeURIComponent(
    clientId,
  )}/${encodeURIComponent(String(reportId))}`;

  return requestJson(url, { method: 'DELETE' });
}
