// utils/ClientService.js
export async function handleUpdate(payload) {
  const res = await fetch(
    'http://localhost:8089/client-sysprin-writer/api/client/update',
    {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(payload),
    }
  );

  if (!res.ok) {
    const text = await res.text();
    throw new Error(`Update failed: ${res.status} ${text}`);
  }

  // Be robust to either JSON or text (some of your endpoints return text)
  const ct = res.headers.get('content-type') || '';
  if (ct.includes('application/json')) {
    const saved = await res.json();
    return saved;                        // <-- normalized JSON
  }

  // Fallback: backend returned text; treat current payload as the "saved" object
  const message = await res.text();
  return { ...payload, _message: message }; // <-- ensure we always return an object with .client
}
