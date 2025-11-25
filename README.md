// ClientIntegrationService.jsx
// Utilities for talking to the backend (gateway + reader + search-integration)

const GATEWAY_BASE_URL =
  import.meta.env?.VITE_GATEWAY_BASE_URL || 'http://localhost:8089';

/**
 * Helper to build a URL with query params.
 */
function buildUrl(path, params = {}) {
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
export async function fetchJson(url, options = {}) {
  const res = await fetch(url, {
    headers: {
      'Accept': 'application/json',
      'Content-Type': 'application/json',
      ...(options.headers || {}),
    },
    ...options,
  });

  if (!res.ok) {
    const text = await res.text().catch(() => '');
    const error = new Error(
      `HTTP ${res.status} ${res.statusText} for ${url} :: ${text || 'No body'}`
    );
    error.status = res.status;
    error.body = text;
    throw error;
  }

  const contentType = res.headers.get('content-type') || '';
  if (contentType.includes('application/json')) {
    return res.json();
  }
  return res.text();
}

/**
 * Autocomplete search for clients.
 *
 * Backend (adjust if your path is different):
 *   GET /search-integration/api/client-autocomplete?keyword=...
 *
 * Used by: ClientAutoCompleteInputBox
 */
export async function fetchClientSuggestions(keyword) {
  if (!keyword || !keyword.trim()) return [];

  const url = buildUrl('/search-integration/api/client-autocomplete', {
    keyword: keyword.trim(),
  });

  const data = await fetchJson(url, { method: 'GET' });

  // Some gateways return { data: [...] }, some return []
  if (data && Array.isArray(data.data)) return data.data;
  if (Array.isArray(data)) return data;
  return [];
}

/**
 * Paged list of clients for the left NavigationPanel.
 *
 * Backend example (adjust path if needed):
 *   GET /client-sysprin-reader/api/clients-paging?page={page}&size={size}
 */
export async function fetchClientsPaging(page = 0, size = 5) {
  const url = buildUrl('/client-sysprin-reader/api/clients-paging', {
    page,
    size,
  });

  const data = await fetchJson(url, { method: 'GET' });

  // Could be Page<ClientDTO> or plain array
  if (data && Array.isArray(data.content)) return data.content;
  if (Array.isArray(data)) return data;
  return [];
}

/**
 * Wildcard / multi-page search for clients.
 *
 * You may already have an endpoint for this in your backend.
 * Adjust the path to whatever you implemented. For example:
 *   GET /client-sysprin-reader/api/clients-search?keyword={kw}&page={page}&size={size}
 */
export async function fetchWildcardPage(keyword, page = 0, size = 20) {
  const kw = (keyword || '').trim();
  if (!kw) return [];

  // ❗ Adjust this path to your real wildcard endpoint
  const url = buildUrl('/client-sysprin-reader/api/clients-search', {
    keyword: kw,
    page,
    size,
  });

  const data = await fetchJson(url, { method: 'GET' });

  if (data && Array.isArray(data.content)) return data.content;
  if (Array.isArray(data)) return data;
  return [];
}

/**
 * NEW: fetch full client+sysPrin detail as in your curl:
 *
 * curl -X 'GET' \
 *   'http://localhost:8089/client-sysprin-reader/api/sysprins/detail/{client}/{sysPrin}' \
 *   -H 'accept: application/json'
 *
 * The API returns either:
 *   [ { ...bigJsonObject... } ]
 * or:
 *   { ...bigJsonObject... }
 *
 * We normalize to a single object.
 */
export async function fetchClientDetail(client, sysPrin) {

  alert("client " + client);
  alert("sysPrin " + sysPrin);

  if (!client) throw new Error('client is required');
  if (!sysPrin) throw new Error('sysPrin is required');

  const path = `/client-sysprin-reader/api/sysprins/detail/${encodeURIComponent(
    client
  )}/${encodeURIComponent(sysPrin)}`;

  const url = new URL(path, GATEWAY_BASE_URL).toString();
  const data = await fetchJson(url, { method: 'GET' });

  alert("data " + data);

  if (Array.isArray(data)) {
    return data[0] || null;
  }
  return data || null;
}
