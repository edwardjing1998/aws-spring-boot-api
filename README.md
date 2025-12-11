  const deleteCall = async (url) => {
    const res = await fetch(url, { method: 'DELETE', headers: { accept: '*/*' } });
    if (!res.ok) throw new Error(await res.text());
    return null;
  };


  else if (mode === 'delete') {
        if (!clientId || !rid) {
             throw new Error("Missing Client ID or Report ID for deletion.");
        }
        await deleteCall(
           `http://localhost:8089/client-sysprin-writer/api/client/reportOptions/delete/${encodeURIComponent(clientId)}/${encodeURIComponent(rid)}`
        );
        onDelete?.(); // notify parent
        setStatusMessage({ severity: 'success', text: 'Report deleted successfully.' });
      }
