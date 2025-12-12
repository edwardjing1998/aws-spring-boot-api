useEffect(() => {
  if (!clientId) return;

  // (Optional) Skip fetch if we are on page 0 and already have data from props
  if (page === 0 && data && data.length > 0 && data[0].clientId === clientId) {
      setReports(data);
      return; 
  }

  const fetchData = async () => {
    // ✅ Use the service function here
    const result = await fetchPreviewClientReports(clientId, page, PAGE_SIZE);
    setReports(result);
  };

  fetchData();
}, [page, clientId, data]);
