export interface SysPrinDTO {
  client: string;
  sysPrin: string;
  // plus all other fields from the backend
  [key: string]: any;
}

/**
 * Shape of the data consumed by the UI.
 * We map the backend response to this structure.
 */
export interface SysPrinPageResponse {
  items: SysPrinDTO[];   // list of SysPrins for this page
  page: number;          // current page index (0-based)
  size: number;          // page size
  total: number;         // total number of SysPrins for this client
}

/**
 * Internal interface representing the raw JSON structure from your backend.
 * Based on the sample: { content: [], page: 0, size: 10, totalElements: 18, totalPages: 2 }
 */
interface BackendSysPrinResponse {
  content: SysPrinDTO[];
  page: number;
  size: number;
  totalElements: number;
  totalPages: number;
}

/**
 * Fetch paged SysPrins for a client.
 *
 * GET /sysprins/client/{clientId}/paged?page={page}&size={size}
 */
export async function fetchSysPrinsByClientPaged(
  clientId: string,
  page: number,
  size: number,
): Promise<SysPrinPageResponse> {
  const url = `http://localhost:8089/client-sysprin-reader/api/sysprins/client/${encodeURIComponent(
    clientId,
  )}/paged?page=${encodeURIComponent(page)}&size=${encodeURIComponent(size)}`;

  const resp = await fetch(url, {
    method: 'GET',
    headers: { accept: '*/*' },
  });

  if (!resp.ok) {
    const text = await resp.text().catch(() => '');
    throw new Error(
      `Failed to fetch SysPrins for client ${clientId}, page=${page}, size=${size}: ${resp.status} ${text}`,
    );
  }

  const json = await resp.json();

  // 1. Check for the standard Spring Data / Paged structure provided in your example
  if (json && Array.isArray(json.content)) {
    const typed = json as BackendSysPrinResponse;
    return {
      items: typed.content,
      page: typed.page,
      size: typed.size,
      total: typed.totalElements,
    };
  }

  // 2. Fallback: if backend returns plain array
  if (Array.isArray(json)) {
    const arr = json as SysPrinDTO[];
    return {
      items: arr,
      page,
      size,
      total: arr.length,
    };
  }

  // 3. Fallback: Generic mapping if structure is different but has items/total
  return {
    items: json.items ?? [],
    page: typeof json.page === 'number' ? json.page : page,
    size: typeof json.size === 'number' ? json.size : size,
    total: typeof json.total === 'number' ? json.total : (json.items?.length ?? 0),
  };
}
