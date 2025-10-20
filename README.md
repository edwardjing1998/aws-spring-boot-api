if (mode === 'changeAll') {
  // POST http://localhost:8089/client-sysprin-writer/api/clients/{clientId}/sysprins/copy-from/{templateSysPrin}
  if (!client) { alert('Client is required.'); return; }
  if (!currentSysPrin) { alert('Template Sys/Prin is required.'); return; }

  const url = `http://localhost:8089/client-sysprin-writer/api/clients/${encodeURIComponent(client)}/sysprins/copy-from/${encodeURIComponent(currentSysPrin)}`;

  const res = await fetch(url, {
    method: 'POST',
    headers: { accept: '*/*' },  // optional, matches your curl
    body: ''                     // empty body, matches your curl
  });

  if (!res.ok) {
    const text = await res.text().catch(() => '');
    throw new Error(`Change All failed (HTTP ${res.status}). ${text}`);
  }
  alert('Change All completed successfully.');
  return;
}
