// ---------- UPDATE (mode === 'edit') ----------
const handleUpdate = async () => {
  try {
    // assumes buildPayload() returns { client, sysPrin, body }
    const { client, sysPrin, body } = buildPayload();

    // PUT http://localhost:8089/client-sysprin-writer/api/sysprins/{client}/{sysPrin}
    const url =
      `http://localhost:8089/client-sysprin-writer/api/sysprins/` +
      `${encodeURIComponent(client)}/${encodeURIComponent(sysPrin)}`;

    const res = await fetch(url, {
      method: 'PUT',
      headers: {
        accept: '*/*',
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        // ensure client/sysPrin are included in the JSON body (as in your curl)
        client,
        sysPrin,
        ...body,
      }),
    });

    if (!res.ok) {
      const text = await res.text().catch(() => '');
      throw new Error(`Update failed (HTTP ${res.status}). ${text}`);
    }

    const saved = await res.json().catch(() => null);
    alert('Updated successfully.');
    if (saved && typeof saved === 'object') {
      setSelectedData(prev => ({ ...prev, ...saved }));
    }
  } catch (err) {
    console.error('Update error:', err);
    alert(`Update error: ${err.message ?? err}`);
  }
};
