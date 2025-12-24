import environment from '../../Environments/Environment';

const CLIENT_SYSPRIN_READER_API_BASE = environment.clientReaderBaseUrl;
const CLIENT_SYSPRIN_WRITER_API_BASE = environment.clientWriterBaseUrl;
const CLIENT_SEARCH_INTEGRATION_API_BASE = environment.clientSearchIntegrationBaseUrl;


// Utilities for talking to the backend (gateway + reader + search-integration)

// Replace import.meta with process.env if using CRA/Webpack, or just hardcode for now to fix the crash
// const GATEWAY_BASE_URL: string = process.env.REACT_APP_GATEWAY_BASE_URL || 'http://localhost:8089';
const GATEWAY_BASE_URL: string = 'http://localhost:8089';

/**
 * Helper type for query params.
 */
type QueryParams = Record<string, string | number | boolean | null | undefined>;

/**
 * Helper to build a URL with query params.
 */
function buildUrl(path: string, params: QueryParams = {}): string {
  const url = new URL(path, GATEWAY_BASE_URL);
  Object.entries(params).forEach(([key, value]) => {
    if (value !== undefined && value !== null) {
      url.searchParams.append(key, String(value));
    }
  });
  return url.toString();
}

/**
 * Generic fetch wrapper.
 * - Throws on non-2xx
 * - Returns JSON if possible, otherwise text.
 */
export async function fetchJson<T = unknown>(
  url: string,
  options: RequestInit = {}
): Promise<T | string> {
  const res = await fetch(url, {
    headers: {
      Accept: 'application/json',
      'Content-Type': 'application/json',
      ...(options.headers || {}),
    },
    ...options,
  });

  if (!res.ok) {
    const text = await res.text().catch(() => '');
    const error: any = new Error(
      `HTTP ${res.status} ${res.statusText} for ${url} :: ${text || 'No body'}`
    );
    error.status = res.status;
    error.body = text;
    throw error;
  }

  const contentType = res.headers.get('content-type') || '';
  if (contentType.includes('application/json')) {
    // typed as T | unknown, but at runtime this is whatever server returns
    return (await res.json()) as T;
  }
  return res.text();
}

/**
 * Shape for a client suggestion item (from autocomplete).
 * Adjust fields if your backend returns more.
 */
export interface ClientSuggestion {
  client: string;
  name: string;
  [key: string]: any;
}

/**
 * Shape for a full Client object (simplified).
 * Expand as you like.
 */
export interface ClientDTO {
  client: string;
  name?: string;
  billingSp?: string;
  reportOptions?: any[];
  sysPrinsPrefixes?: any[];
  clientEmail?: any[];
  sysPrins?: any[];
  [key: string]: any;
}

/**
 * Autocomplete search for clients.
 *
 * Backend (adjust if your path is different):
 * GET /search-integration/api/client-autocomplete?keyword=...
 *
 * Used by: ClientAutoCompleteInputBox
 */
export async function fetchClientSuggestions(
  keyword: string
): Promise<ClientSuggestion[]> {
  if (!keyword || !keyword.trim()) return [];

  const url = buildUrl('/search-integration/api/client-autocomplete', {
    keyword: keyword.trim(),
  });

  const data = await fetchJson<any>(url, { method: 'GET' });

  // Some gateways return { data: [...] }, some return []
  if (data && Array.isArray((data as any).data)) return (data as any).data;
  if (Array.isArray(data)) return data as ClientSuggestion[];
  return [];
}

/**
 * Paged list of clients for the left NavigationPanel.
 *
 * Backend example (adjust path if needed):
 * GET /client-sysprin-reader/api/clients-paging?page={page}&size={size}
 */
export async function fetchClientsPaging(
  page: number = 0,
  size: number = 5
): Promise<ClientDTO[]> {
  // const url = buildUrl('/client-sysprin-reader/api/clients-paging', {
  const url = buildUrl('/client-sysprin-reader/api/client/details', {
    page,
    size,
  });

  const data = await fetchJson<any>(url, { method: 'GET' });

  // Could be Page<ClientDTO> or plain array
  if (data && Array.isArray(data.content)) return data.content as ClientDTO[];
  if (Array.isArray(data)) return data as ClientDTO[];
  return [];
}

/**
 * Wildcard / multi-page search for clients.
 *
 * Example backend:
 * GET /client-sysprin-reader/api/clients-search?keyword={kw}&page={page}&size={size}
 */
export async function fetchWildcardPage(
  keyword: string,
  page: number = 0,
  size: number = 20
): Promise<ClientDTO[]> {
  const kw = (keyword || '').trim();
  if (!kw) return [];

  const url = buildUrl('/client-sysprin-reader/api/clients-search', {
    keyword: kw,
    page,
    size,
  });

  const data = await fetchJson<any>(url, { method: 'GET' });

  if (data && Array.isArray(data.content)) return data.content as ClientDTO[];
  if (Array.isArray(data)) return data as ClientDTO[];
  return [];
}

/**
 * Fetch full client detail (client + sysPrins + emails + reportOptions)
 *
 * Calls:
 * GET /client-sysprin-reader/api/client/{client}
 *
 * Example:
 * GET /client-sysprin-reader/api/client/0003
 *
 * Response shape (your example):
 * [
 * {
 * client: "0003",
 * name: "...",
 * reportOptions: [...],
 * sysPrins: [...],
 * clientEmail: [...],
 * ...
 * }
 * ]
 *
 * We normalize this to a single object:
 * { client: "0003", ... }
 */
export async function fetchClientDetail(client: string): Promise<ClientDTO | null> {
  if (!client) throw new Error('client is required');

  const path = `/client-sysprin-reader/api/client/${encodeURIComponent(client)}?page=0&size=10`;
  const url = new URL(path, GATEWAY_BASE_URL).toString();

  const data = await fetchJson<any>(url, { method: 'GET' });

  if (Array.isArray(data)) {
    return (data[0] as ClientDTO) || null;
  }
  return (data as ClientDTO) || null;
}

export const fetchClientReportSuggestions = async (keyword: string): Promise<any> => {
  if (!keyword || !keyword.trim()) return [];

  const encoded = encodeURIComponent(keyword.trim());
  const path = keyword.trim().endsWith('*')
    ? `/client-sysprin-reader/api/client/wildcard?keyword=${encoded}`
    : `/search-integration/api/report-autocomplete?keyword=${encoded}`;
  
  const url = new URL(path, GATEWAY_BASE_URL).toString();
  return fetchJson(url, { method: 'GET' });
};
