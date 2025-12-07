/* ... inside sysPrins JSON_QUERY ... */
FROM (
    SELECT 
        paginated_sp.*,
        ROW_NUMBER() OVER (
            PARTITION BY paginated_sp.sys_prin 
            ORDER BY paginated_sp.active DESC, paginated_sp.sys_prin
        ) as rn
    FROM (
        /* ✅ STEP 1: PAGINATE FIRST (Get raw slice) */
        SELECT *
        FROM sys_prins
        WHERE client = c.client
        ORDER BY sys_prin
        OFFSET :sysPrinOffset ROWS FETCH NEXT :sysPrinSize ROWS ONLY
    ) paginated_sp
) sp
/* ✅ STEP 2: DEDUP SECOND (Filter within that slice) */
WHERE sp.rn = 1
ORDER BY sp.sys_prin
FOR JSON PATH
