if (mode === 'duplicate') {
  if (!client) { alert('Client is required.'); return; }
  if (!currentSysPrin) { alert('Source Sys/Prin is required.'); return; }
  if (!targetSysPrin.trim()) { alert('Target Sys/Prin is required.'); return; }

  const base = 'http://localhost:8089/client-sysprin-writer/api';
  const url =
    `${base}/clients/${encodeURIComponent(client)}` +
    `/sysprins/${encodeURIComponent(currentSysPrin)}` +
    `/duplicate-to/${encodeURIComponent(targetSysPrin.trim())}` +
    `?overwrite=true&copyAreas=${copyAreas}`;

  const res = await fetch(url, { method: 'POST', mode: 'cors' }); // no body
  if (!res.ok) {
    const text = await res.text().catch(() => '');
    throw new Error(`Duplicate failed (HTTP ${res.status}). ${text}`);
  }
  alert('Duplicate completed successfully.');
  return;
}
