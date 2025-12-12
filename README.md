// API Fetch for Server-Side Pagination
  useEffect(() => {
    if (!clientId) return;

    const fetchData = async () => {
      try {
        // Use the imported service function
        // It returns Promise<ClientReportRow[]>, so we can await it directly
        const nextRows = await fetchClientReports(clientId, page, PAGE_SIZE);
        
        setTableData(nextRows);
      } catch (error) {
        console.error("Error fetching client reports:", error);
        setTableData([]);
      }
    };

    fetchData();
  }, [clientId, page, refreshKey]);
