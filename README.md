if (mode === 'move') {
  // PUT http://localhost:8089/client-sysprin-writer/api/sysprins/move-sysprin?oldClientId={client}&sysPrin={currentSysPrin}&newClientId={newClient}
  if (!currentSysPrin) { alert('Sys/Prin is required.'); return; }
  if (!client) { alert('Old Client is required.'); return; }
  if (!newClient.trim()) { alert('New Client is required.'); return; }

  const base = 'http://localhost:8089/client-sysprin-writer/api/sysprins/move-sysprin';
  const url =
    `${base}?oldClientId=${encodeURIComponent(client)}` +
    `&sysPrin=${encodeURIComponent(currentSysPrin)}` +
    `&newClientId=${encodeURIComponent(newClient.trim())}`;

  try {
    const res = await fetch(url, {
      method: 'PUT',
      headers: { accept: '*/*' }, // match curl
      body: '' // empty body, same as curl
    });

    if (!res.ok) {
      const text = await res.text().catch(() => '');
      throw new Error(`Move failed (HTTP ${res.status}). ${text}`);
    }

    alert('Move completed successfully.');
  } catch (err) {
    console.error('Move error:', err);
    alert(err?.message ?? String(err));
  }
  return;
}
