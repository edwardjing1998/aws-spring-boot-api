useEffect(() => {
  if (!clientId) return;

  const fetchData = async () => {
    try {
      const result = await fetchPreviewClientReports(clientId, page, PAGE_SIZE);
      setReports(result);
    } catch (e) {
      console.error('fetchPreviewClientReports failed:', e);
      setReports([]);
    }
  };

  fetchData();
}, [clientId, page, totalCount]);
