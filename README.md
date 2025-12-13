    const fetchData = async () => {
      // 3. Use Service Logic
      // The service handles errors internally and returns [] on failure
      const nextRows = await fetchAtmCashPrefixes(clientId, page, PAGE_SIZE);
      setPrefixes(nextRows);
    };
