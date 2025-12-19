import { AtmCashPrefixRow } from './EditAtmCashPrefix.types';

const WRITE_URL = 'http://localhost:8089/client-sysprin-writer/api/prefix';
const READ_URL = 'http://localhost:8089/client-sysprin-reader/api/client/sysprin-prefix';
const clientId = '0042';
const page = 0;
const pageSize = 10

/**
 * Fetches paginated ATM/Cash prefixes for a specific client/billingSp.
 */
export const fetchEditAtmCashPrefixes = async (
  clientId: string,
  page: number,
  pageSize: number
): Promise<AtmCashPrefixRow[]> => {
  try {
    const url = `${READ_URL}/${encodeURIComponent(clientId)}/pagination?page=${page}&size=${pageSize}`;
    const resp = await fetch(url, {
      method: 'GET',
      headers: { accept: '*/*' },
    });

    if (resp.ok) {
      const json = await resp.json();
      // Handle both direct array response or Spring Page content object
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


/**
 * Creates a new ATM/Cash prefix.
 */
export const createAtmCashPrefix = async (data: AtmCashPrefixRow): Promise<void> => {
  const res = await fetch(`${WRITE_URL}/add`, {
    method: 'POST',
    headers: { accept: '*/*', 'Content-Type': 'application/json' },
    body: JSON.stringify({
      billingSp: data.billingSp ?? '',
      prefix: data.prefix ?? '',
      atmCashRule: String(data.atmCashRule ?? ''),
    }),
  });

  if (!res.ok) {
    const text = await res.text().catch(() => '');
    throw new Error(`${res.status} ${res.statusText}${text ? ` - ${text}` : ''}`);
  }
};

/**
 * Updates an existing ATM/Cash prefix.
 */
export const updateAtmCashPrefix = async (data: AtmCashPrefixRow): Promise<void> => {
  const res = await fetch(`${WRITE_URL}/update`, {
    method: 'PUT',
    headers: { accept: '*/*', 'Content-Type': 'application/json' },
    body: JSON.stringify({
      billingSp: data.billingSp ?? '',
      prefix: data.prefix ?? '',
      atmCashRule: String(data.atmCashRule ?? ''),
    }),
  });

  if (!res.ok) {
    const text = await res.text().catch(() => '');
    throw new Error(`${res.status} ${res.statusText}${text ? ` - ${text}` : ''}`);
  }
};

/**
 * Deletes an ATM/Cash prefix.
 */
export const deleteAtmCashPrefix = async (data: AtmCashPrefixRow): Promise<void> => {
  const res = await fetch(`${WRITE_URL}/delete`, {
    method: 'DELETE',
    headers: { accept: '*/*', 'Content-Type': 'application/json' },
    body: JSON.stringify({
      billingSp: data.billingSp ?? '',
      prefix: data.prefix ?? '',
      atmCashRule: String(data.atmCashRule ?? ''),
    }),
  });

  if (!res.ok) {
    const text = await res.text().catch(() => '');
    throw new Error(`${res.status} ${res.statusText}${text ? ` - ${text}` : ''}`);
  }
};
