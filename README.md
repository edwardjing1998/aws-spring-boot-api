// src/views/sys-prin-configuration/utils/SysPrinIntegrationService.ts

export interface SysPrinDTO {
  client: string;
  sysPrin: string;
  // keep it open for all other fields the backend sends:
  [key: string]: any;
}

/**
 * Fetch SysPrins for a client.
 *
 * GET /client-sysprin-reader/api/sysprins/client/{clientId}
 * Response: SysPrinDTO[]
 */
export async function fetchSysPrinsByClient(
  clientId: string,
): Promise<SysPrinDTO[]> {
  const url = `http://localhost:8089/client-sysprin-reader/api/sysprins/client/${encodeURIComponent(
    clientId,
  )}`;

  const resp = await fetch(url, {
    method: 'GET',
    headers: { accept: '*/*' },
  });

  if (!resp.ok) {
    const text = await resp.text().catch(() => '');
    throw new Error(
      `Failed to fetch SysPrins for client ${clientId}: ${resp.status} ${text}`,
    );
  }

  const json = (await resp.json()) as SysPrinDTO[];

  // ensure it's always an array
  return Array.isArray(json) ? json : [];
}
