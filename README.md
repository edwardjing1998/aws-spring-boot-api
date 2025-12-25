// SysPrinInformationWindow.service.ts
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

/** POST /sysprins/create/{client}/{sysPrin} */
export async function createSysPrin(client: string, sysPrin: string, payload: any): Promise<any> {
  const url = `${CLIENT_SYSPRIN_WRITER_API_BASE}/sysprins/create/${encodeURIComponent(
    client
  )}/${encodeURIComponent(sysPrin)}`;

  const res = await fetch(url, {
    method: 'POST',
    headers: { accept: '*/*', 'Content-Type': 'application/json' },
    body: JSON.stringify(payload ?? {}),
  });

  if (!res.ok) throw new Error(await parseError(res, 'Create failed'));

  // may or may not return json
  try {
    const ct = res.headers.get('Content-Type') || '';
    if (ct.includes('application/json')) return await res.json();
  } catch {}
  return null;
}

/** PUT /sysprins/update/{client}/{sysPrin} */
export async function updateSysPrin(client: string, sysPrin: string, payload: any): Promise<any> {
  const url = `${CLIENT_SYSPRIN_WRITER_API_BASE}/sysprins/update/${encodeURIComponent(
    client
  )}/${encodeURIComponent(sysPrin)}`;

  const res = await fetch(url, {
    method: 'PUT',
    headers: { accept: '*/*', 'Content-Type': 'application/json' },
    body: JSON.stringify(payload ?? {}),
  });

  if (!res.ok) throw new Error(await parseError(res, 'Update failed'));

  try {
    const ct = res.headers.get('Content-Type') || '';
    if (ct.includes('application/json')) return await res.json();
  } catch {}
  return null;
}

/** DELETE /sysprins/{client}/{sysPrin} */
export async function deleteSysPrin(client: string, sysPrin: string): Promise<void> {
  const url = `${CLIENT_SYSPRIN_WRITER_API_BASE}/sysprins/${encodeURIComponent(
    client
  )}/${encodeURIComponent(sysPrin)}`;

  const res = await fetch(url, {
    method: 'DELETE',
    headers: { accept: '*/*' },
  });

  if (!res.ok) throw new Error(await parseError(res, 'Delete Sys/Prin failed'));
}

/** PUT /sysprins/move-sysprin?oldClientId=&sysPrin=&newClientId= */
export async function moveSysPrin(oldClientId: string, sysPrin: string, newClientId: string): Promise<void> {
  const url =
    `${CLIENT_SYSPRIN_WRITER_API_BASE}/sysprins/move-sysprin` +
    `?oldClientId=${encodeURIComponent(oldClientId)}` +
    `&sysPrin=${encodeURIComponent(sysPrin)}` +
    `&newClientId=${encodeURIComponent(newClientId)}`;

  const res = await fetch(url, {
    method: 'PUT',
    headers: { accept: '*/*' },
  });

  if (!res.ok) throw new Error(await parseError(res, 'Move failed'));
}

/** POST /clients/{clientId}/sysprins/{source}/duplicate-to/{target}?overwrite=false&copyAreas=&copyVendorSentTo= */
export async function duplicateSysPrin(
  clientId: string,
  sourceSysPrin: string,
  targetSysPrin: string,
  opts: { overwrite?: boolean; copyAreas?: boolean; copyVendorSentTo?: boolean } = {}
): Promise<any> {
  const overwrite = opts.overwrite ?? false;
  const copyAreas = opts.copyAreas ?? true;
  const copyVendorSentTo = opts.copyVendorSentTo ?? true;

  const url =
    `${CLIENT_SYSPRIN_WRITER_API_BASE}/clients/${encodeURIComponent(clientId)}` +
    `/sysprins/${encodeURIComponent(sourceSysPrin)}` +
    `/duplicate-to/${encodeURIComponent(targetSysPrin)}` +
    `?overwrite=${encodeURIComponent(String(overwrite))}` +
    `&copyAreas=${encodeURIComponent(String(copyAreas))}` +
    `&copyVendorSentTo=${encodeURIComponent(String(copyVendorSentTo))}`;

  const res = await fetch(url, {
    method: 'POST',
    headers: { accept: '*/*' },
    body: '',
  });

  if (!res.ok) throw new Error(await parseError(res, 'Duplicate failed'));

  try {
    const ct = res.headers.get('Content-Type') || '';
    if (ct.includes('application/json')) return await res.json();
  } catch {}
  return null;
}

/** POST /clients/{clientId}/sysprins/copy-from/{sourceSysPrin} */
export async function changeAllSysPrin(clientId: string, sourceSysPrin: string): Promise<any> {
  const url =
    `${CLIENT_SYSPRIN_WRITER_API_BASE}/clients/${encodeURIComponent(clientId)}` +
    `/sysprins/copy-from/${encodeURIComponent(sourceSysPrin)}`;

  const res = await fetch(url, {
    method: 'POST',
    headers: { accept: '*/*' },
    body: '',
  });

  if (!res.ok) throw new Error(await parseError(res, 'Change All failed'));

  try {
    const ct = res.headers.get('Content-Type') || '';
    if (ct.includes('application/json')) return await res.json();
  } catch {}
  return null;
}
