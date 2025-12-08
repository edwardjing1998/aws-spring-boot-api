                          (SELECT COUNT(*) 
                           FROM sys_prins sp_cnt 
                           WHERE sp_cnt.client = c.client
                          ) AS [sysPrinTotal],
