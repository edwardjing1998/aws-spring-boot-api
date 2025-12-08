WITH CTE AS (
    SELECT *,
           ROW_NUMBER() OVER (PARTITION BY client ORDER BY (SELECT NULL)) as rn
    FROM clients
    WHERE client = '0048'
)
SELECT *
FROM CTE
WHERE rn = 1;
