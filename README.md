@Query(value = """
    SELECT x.*
    FROM (
        SELECT v.*,
               ROW_NUMBER() OVER (
                   PARTITION BY v.SYS_PRIN
                   ORDER BY v.VENDOR_ID
               ) AS rn
        FROM VENDOR_RECEIVED_FROM v
        WHERE v.SYS_PRIN IN (:sysPrins)
    ) x
    WHERE x.rn <= 10
    """, nativeQuery = true)
List<VendorReceivedFrom> findTop10PerSysPrin(@Param("sysPrins") Collection<String> sysPrins);
