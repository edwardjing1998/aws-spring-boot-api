import environment from '../../Environments/Environment'

const CLIENT_SYSPRIN_READER_API_BASE = environment.clientReaderBaseUrl
const CLIENT_SYSPRIN_WRITER_API_BASE = environment.clientWriterBaseUrl
const CLIENT_SEARCH_INTEGRATION_API_BASE = environment.clientSearchIntegrationBaseUrl

/**
 * Helper type for query params.
 */
type QueryParams = Record<string, string | number | boolean | null | undefined>

/**
 * Build a URL from a base + path + query params.
 * - base should be like: http://localhost:8089/client-sysprin-reader/api
 * - path should be like: /client/details
 */
function buildUrl(base: string, path: string, params: QueryParams = {}): string {
  // ensure base ends without trailing slash for consistent joining
  const cleanBase = base.replace(/\/+$/, '')
  const cleanPath = path.startsWith('/') ? path : `/${path}`

  const url = new URL(`${cleanBase}${cleanPath}`)

  Object.entries(params).forEach(([key, value]) => {
    if (value !== undefined && value !== null) {
      url.searchParams.set(key, String(value))
    }
  })

  return url.toString()
}

/**
 * Generic fetch wrapper.
 * - Throws on non-2xx
 * - Returns JSON if possible, otherwise text.
 */
export async function fetchJson<T = unknown>(
  url: string,
  options: RequestInit = {},
): Promise<T | string> {
  const res = await fetch(url, {
    ...options,
    headers: {
      Accept: 'application/json',
      ...(options.headers || {}),
    },
  })

  if (!res.ok) {
    const text = await res.text().catch(() => '')
    const error: any = new Error(
      `HTTP ${res.status} ${res.statusText} for ${url} :: ${text || 'No body'}`,
    )
    error.status = res.status
    error.body = text
    throw error
  }

  const contentType = res.headers.get('content-type') || ''
  if (contentType.includes('application/json')) {
    return (await res.json()) as T
  }
  return res.text()
}

/**
 * Shape for a client suggestion item (from autocomplete).
 */
export interface ClientSuggestion {
  client: string
  name: string
  [key: string]: any
}

/**
 * Shape for a full Client object (simplified).
 */
export interface ClientDTO {
  client: string
  name?: string
  billingSp?: string
  reportOptions?: any[]
  sysPrinsPrefixes?: any[]
  clientEmail?: any[]
  sysPrins?: any[]
  [key: string]: any
}

/**
 * Autocomplete search for clients.
 * GET {clientSearchIntegrationBaseUrl}/client-autocomplete?keyword=...
 */
export async function fetchClientSuggestions(keyword: string): Promise<ClientSuggestion[]> {
  const kw = (keyword || '').trim()
  if (!kw) return []

  const url = buildUrl(CLIENT_SEARCH_INTEGRATION_API_BASE, '/client-autocomplete', {
    keyword: kw,
  })

  const data = await fetchJson<any>(url, { method: 'GET' })

  // Some APIs return { data: [...] }, some return [...]
  if (data && Array.isArray((data as any).data)) return (data as any).data
  if (Array.isArray(data)) return data as ClientSuggestion[]
  return []
}

/**
 * Paged list of clients.
 * GET {clientReaderBaseUrl}/client/details?page={page}&size={size}
 */
export async function fetchClientsPaging(page: number = 0, size: number = 5): Promise<ClientDTO[]> {
  const url = buildUrl(CLIENT_SYSPRIN_READER_API_BASE, '/client/details', { page, size })

  const data = await fetchJson<any>(url, { method: 'GET' })

  // Could be Page<ClientDTO> or plain array
  if (data && Array.isArray((data as any).content)) return (data as any).content as ClientDTO[]
  if (Array.isArray(data)) return data as ClientDTO[]
  return []
}

/**
 * Wildcard / multi-page search for clients.
 * GET {clientReaderBaseUrl}/clients-search?keyword={kw}&page={page}&size={size}
 */
export async function fetchWildcardPage(
  keyword: string,
  page: number = 0,
  size: number = 20,
): Promise<ClientDTO[]> {
  const kw = (keyword || '').trim()
  if (!kw) return []

  const url = buildUrl(CLIENT_SYSPRIN_READER_API_BASE, '/clients-search', {
    keyword: kw,
    page,
    size,
  })

  const data = await fetchJson<any>(url, { method: 'GET' })

  if (data && Array.isArray((data as any).content)) return (data as any).content as ClientDTO[]
  if (Array.isArray(data)) return data as ClientDTO[]
  return []
}

/**
 * Fetch full client detail
 * GET {clientReaderBaseUrl}/client/{client}?page=0&size=10
 *
 * If backend returns array, we normalize to first element.
 */
export async function fetchClientDetail(client: string): Promise<ClientDTO | null> {
  const c = (client || '').trim()
  if (!c) throw new Error('client is required')

  const url = buildUrl(CLIENT_SYSPRIN_READER_API_BASE, `/client/${encodeURIComponent(c)}`, {
    page: 0,
    size: 10,
  })

  const data = await fetchJson<any>(url, { method: 'GET' })

  if (Array.isArray(data)) return (data[0] as ClientDTO) || null
  return (data as ClientDTO) || null
}

/**
 * Report suggestions (autocomplete)
 * - If keyword ends with '*': GET {clientReaderBaseUrl}/client/wildcard?keyword=...
 * - Else:                GET {searchIntegrationBaseUrl}/report-autocomplete?keyword=...
 */
export async function fetchClientReportSuggestions(keyword: string): Promise<any> {
  const kw = (keyword || '').trim()
  if (!kw) return []

  const encoded = kw // buildUrl will encode query param value safely

  if (kw.endsWith('*')) {
    const url = buildUrl(CLIENT_SYSPRIN_READER_API_BASE, '/client/wildcard', { keyword: encoded })
    return fetchJson(url, { method: 'GET' })
  }

  const url = buildUrl(CLIENT_SEARCH_INTEGRATION_API_BASE, '/report-autocomplete', {
    keyword: encoded,
  })
  return fetchJson(url, { method: 'GET' })
}
