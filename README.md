/* ✅ NEW: Add this count for Client Report Options */
(SELECT COUNT(*) 
 FROM CLIENT_REPORT_OPTIONS cro_cnt 
 WHERE cro_cnt.client_id = c.client
) AS [reportOptionTotal],
