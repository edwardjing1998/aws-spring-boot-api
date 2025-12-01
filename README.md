// src/views/sys-prin-configuration/utils/SysPrinIntegrationService.ts

export interface SysPrinDTO {
  client: string;
  sysPrin: string;
  // plus all other fields from the backend
  [key: string]: any;
}

/**
 * Shape of your Java `SysPrinPaginationResponse<SysPrinDTO>`.
 * Adjust field names here if your JSON differs.
 */
export interface SysPrinPageResponse {
  items: SysPrinDTO[];   // list of SysPrins for this page
  page: number;          // current page index (0-based)
  size: number;          // page size
  total: number;         // total number of SysPrins for this client
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

  // Fallback: if backend still returns plain array, wrap it.
  if (Array.isArray(json)) {
    const arr = json as SysPrinDTO[];
    return {
      items: arr,
      page,
      size,
      total: arr.length,
    };
  }

  // Normal: assume backend sends { items, page, size, total }
  const typed = json as SysPrinPageResponse;
  return {
    items: typed.items ?? [],
    page: typeof typed.page === 'number' ? typed.page : page,
    size: typeof typed.size === 'number' ? typed.size : size,
    total: typeof typed.total === 'number' ? typed.total : (typed.items?.length ?? 0),
  };
}
