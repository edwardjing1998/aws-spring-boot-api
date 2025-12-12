/* ✅ ADD THIS BLOCK HERE */
        (SELECT COUNT(DISTINCT client) 
         FROM clients 
         WHERE client IS NOT NULL AND client != '' AND name != ''
        ) AS [clientTotal],
