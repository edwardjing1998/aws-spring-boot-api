/* ✅ BEST PRACTICE: Add this subquery here.
                       It calculates the total number of sys_prins for the client
                       efficiently without fetching all rows. */
                    (SELECT COUNT(*) 
                     FROM sys_prins sp_cnt 
                     WHERE sp_cnt.client = c.client
                    ) AS [sysPrinTotal],
