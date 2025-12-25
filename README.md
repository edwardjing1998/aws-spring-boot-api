// EditFileSentTo.service.ts

export const CLIENT_SYSPRIN_READER_API_BASE =
  'http://localhost:8089/client-sysprin-reader/api';

export const CLIENT_SYSPRIN_WRITER_API_BASE =
  'http://localhost:8089/client-sysprin-writer/api';

async function parseError(res: Response, fallback: string) {
  let msg = `${fallback} (${res.status})`;
  try {
    const ct = res.headers.get('Content-Type') || '';
    if (ct.includes('application/json')) {
      const j = await res.json();
      msg = (j as any)?.message || JSON.stringify(j);
    } else {
      const t = await res.text();
      if (t) msg = t;
    }
  } catch {
    // ignore
  }
  return msg;
}

/**
 * GET /vendor?fileIo=I
 * Returns raw vendor list (caller can normalize)
 */
export async function fetchVendorsForSentTo(fileIo: string = 'I'): Promise<any[]> {
  const url = `${CLIENT_SYSPRIN_READER_API_BASE}/vendor?fileIo=${encodeURIComponent(fileIo)}`;
  const res = await fetch(url, { headers: { accept: '*/*' } });

  if (!res.ok) throw new Error(await parseError(res, 'Failed to load vendors (I)'));

  const data = await res.json().catch(() => []);
  return Array.isArray(data) ? data : [];
}

/**
 * POST /sysprins/{sysPrin}/sent-to/create
 * Body: { sysPrin, vendorId, queForMail }
 */
export async function createSentTo(
  sysPrin: string,
  vendorId: string,
  queForMail: boolean,
): Promise<void> {
  const url = `${CLIENT_SYSPRIN_WRITER_API_BASE}/sysprins/${encodeURIComponent(
    sysPrin,
  )}/sent-to/create`;

  const res = await fetch(url, {
    method: 'POST',
    headers: { accept: '*/*', 'Content-Type': 'application/json' },
    body: JSON.stringify({ sysPrin, vendorId, queForMail }),
  });

  if (!res.ok) throw new Error(await parseError(res, 'Create sent-to failed'));
  // ignore response body (some APIs return text)
}

/**
 * DELETE /sysprins/{sysPrin}/sent-to/delete
 * Body: { sysPrin, vendorId, queForMail }
 */
export async function deleteSentTo(
  sysPrin: string,
  vendorId: string,
  queForMail: boolean,
): Promise<void> {
  const url = `${CLIENT_SYSPRIN_WRITER_API_BASE}/sysprins/${encodeURIComponent(
    sysPrin,
  )}/sent-to/delete`;

  const res = await fetch(url, {
    method: 'DELETE',
    headers: { accept: '*/*', 'Content-Type': 'application/json' },
    body: JSON.stringify({ sysPrin, vendorId, queForMail }),
  });

  if (!res.ok) throw new Error(await parseError(res, 'Delete sent-to failed'));
  // ignore response body
}
