if (mode === 'duplicate') {
  if (!client) { alert('Client is required.'); return; }
  if (!currentSysPrin) { alert('Source Sys/Prin is required.'); return; }
  if (!targetSysPrin.trim()) { alert('Target Sys/Prin is required.'); return; }

  // Use the exact path that works in your cURL
  const base = 'http://localhost:8089/client-sysprin-writer/client-sysprin-writer/api';
  const url =
    `${base}/clients/${encodeURIComponent(client)}` +
    `/sysprins/${encodeURIComponent(currentSysPrin)}` +
    `/duplicate-to/${encodeURIComponent(targetSysPrin.trim())}` +
    `?overwrite=true&copyAreas=${encodeURIComponent(Boolean(copyAreas))}`;

  try {
    const res = await fetch(url, {
      method: 'POST',
      mode: 'cors',                 // keep CORS explicit
      headers: { accept: '*/*' },   // mirrors your curl
      // no body
    });

    if (!res.ok) {
      const text = await res.text().catch(() => '');
      throw new Error(`Duplicate failed (HTTP ${res.status}). ${text}`);
    }

    alert('Duplicate completed successfully.');
  } catch (err) {
    // If you see "TypeError: Failed to fetch", it’s almost always CORS/preflight or a redirect without CORS headers.
    console.error('Duplicate error:', err);
    alert(err?.message ?? String(err));
  }
  return;
}
