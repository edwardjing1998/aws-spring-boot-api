// ---------- CREATE (mode === 'new') ----------
const handleCreate = async () => {
  try {
    const { client, sysPrin, body } = buildPayload();

    // POST http://localhost:8089/client-sysprin-writer/api/sysprins/create/{client}/{sysPrin}
    const url =
      `http://localhost:8089/client-sysprin-writer/api/sysprins/create/` +
      `${encodeURIComponent(client)}/${encodeURIComponent(sysPrin)}`;

    const res = await fetch(url, {
      method: 'POST',
      headers: {
        accept: '*/*',
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        // include these explicitly to mirror your curl body
        client,
        sysPrin,
        ...body,
      }),
    });

    if (!res.ok) {
      const text = await res.text().catch(() => '');
      throw new Error(`Create failed (HTTP ${res.status}). ${text}`);
    }

    const saved = await res.json().catch(() => null);
    alert('Created successfully.');
    if (saved && typeof saved === 'object') {
      setSelectedData(prev => ({ ...prev, ...saved }));
    }
  } catch (err) {
    console.error('Create error:', err);
    alert(`Create error: ${err.message ?? err}`);
  }
};
