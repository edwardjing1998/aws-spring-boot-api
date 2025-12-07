/* ✅ YOUR CHANGE STARTS HERE */
  FROM (
      SELECT 
          *,
          /* 1. Calculate Row Number partitioned by the ID you want unique */
          ROW_NUMBER() OVER (
              PARTITION BY sys_prin 
              ORDER BY active DESC, sys_prin
          ) as rn
      FROM sys_prins
      /* 2. Correlate with the outer 'clients c' table here for performance */
      WHERE client = c.client 
  ) sp
  /* 3. Filter to keep only the 1st instance */
  WHERE sp.rn = 1 
  /* ✅ YOUR CHANGE ENDS HERE */
