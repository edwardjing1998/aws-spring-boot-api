// EditInvalidedArea.service.ts
import type { InvalidAreaResponse } from './EditInvalidedArea.types';

const CLIENT_SYSPRIN_WRITER_API_BASE = 'http://localhost:8089/client-sysprin-writer/api';

/** 通用：兼容 text/json 的错误读取 */
async function readErrorMessage(res: Response): Promise<string> {
  let msg = `Request failed (${res.status})`;
  try {
    const ct = res.headers.get('Content-Type') || '';
    if (ct.includes('application/json')) {
      const j = await res.json();
      msg = (j as any)?.message || JSON.stringify(j);
    } else {
      msg = await res.text();
    }
  } catch {
    // ignore parse errors
  }
  return msg || `Request failed (${res.status})`;
}

/** 通用：成功时尝试解析 JSON，否则返回 null */
async function tryReadJson<T = any>(res: Response): Promise<T | null> {
  try {
    const ct = res.headers.get('Content-Type') || '';
    if (ct.includes('application/json')) return (await res.json()) as T;
  } catch {
    // ignore parse errors
  }
  return null;
}

/** POST /api/sysprins/{sysPrin}/invalid-areas/create  { area, sysPrin } */
export async function createInvalidArea(sysPrin: string, area: string): Promise<InvalidAreaResponse> {
  const url = `${CLIENT_SYSPRIN_WRITER_API_BASE}/sysprins/${encodeURIComponent(
    sysPrin
  )}/invalid-areas/create`;

  const res = await fetch(url, {
    method: 'POST',
    headers: {
      accept: '*/*',
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({ area, sysPrin }),
  });

  if (!res.ok) throw new Error(await readErrorMessage(res));

  // 如果后端返回 JSON，就用它；否则给一个 fallback
  const json = await tryReadJson<InvalidAreaResponse>(res);
  return (
    json ?? {
      id: { sysPrin, area },
      ok: true,
    }
  );
}

/** DELETE /api/sysprins/{sysPrin}/invalid-areas/delete  { area, sysPrin } */
export async function deleteInvalidArea(sysPrin: string, area: string): Promise<{ ok: boolean }> {
  const url = `${CLIENT_SYSPRIN_WRITER_API_BASE}/sysprins/${encodeURIComponent(
    sysPrin
  )}/invalid-areas/delete`;

  const res = await fetch(url, {
    method: 'DELETE',
    headers: {
      accept: '*/*',
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({ area, sysPrin }),
  });

  if (!res.ok) throw new Error(await readErrorMessage(res));

  // 服务端可能返回 text；只要 ok 就认为成功
  const json = await tryReadJson<{ ok?: boolean }>(res);
  return { ok: json?.ok ?? true };
}



EditInvalidedArea.service.ts
