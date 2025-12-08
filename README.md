FROM (
                        SELECT *,
                               ROW_NUMBER() OVER (
                                   PARTITION BY sys_prin 
                                   ORDER BY sys_prin -- Add tie-breaker if needed
                               ) as rn
                        FROM sys_prins
                        WHERE client = c.client
                    ) sp
                    WHERE sp.rn = 1  -- Filter duplicates BEFORE paging
                    
                    ORDER BY sp.sys_prin
                    OFFSET :sysPrinOffset ROWS FETCH NEXT :sysPrinSize ROWS ONLY
