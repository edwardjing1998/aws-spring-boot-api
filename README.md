FROM (
    SELECT *,
           ROW_NUMBER() OVER (PARTITION BY client ORDER BY client) as rn
    FROM clients
    WHERE client IS NOT NULL and client != '' and name != ''
) c
WHERE c.rn = 1
ORDER BY c.client
OFFSET (:page * :size) ROWS FETCH NEXT :size ROWS ONLY
FOR JSON PATH
