import { AtmCashPrefixRow } from '../EditAtmCashPrefix.types';

/**
 * Fetches paginated ATM/Cash prefixes for a specific client.
 */
export const fetchPreviewAtmCashPrefixes = async (
  clientId: string,
  page: number,
  pageSize: number
): Promise<AtmCashPrefixRow[]> => {
  try {
    const url = `http://localhost:8089/client-sysprin-reader/api/client/sysprin-prefix/${encodeURIComponent(clientId)}/pagination?page=${page}&size=${pageSize}`;

    const resp = await fetch(url, {
      method: 'GET',
      headers: { accept: '*/*' },
    });

    if (resp.ok) {
      const json = await resp.json();
      // Normalize response: handle direct array or Page content object
      const nextRows: AtmCashPrefixRow[] = Array.isArray(json) ? json : (json.content || []);
      return nextRows;
    } else {
      console.error("Failed to fetch prefixes");
      return [];
    }
  } catch (error) {
    console.error("Error fetching prefixes:", error);
    return [];
  }
};
