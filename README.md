@Query(value = """
        SELECT * FROM (
            SELECT 
                sp.*, 
                ROW_NUMBER() OVER (
                    PARTITION BY sp.sys_prin 
                    ORDER BY sp.ACTIVE DESC, sp.sys_prin ASC
                ) as rn
            FROM SYS_PRINS sp 
            WHERE sp.client = :client
        ) deduped
        WHERE deduped.rn = 1
        """,
        // This is required for Page<T> to work with the query above
        countQuery = "SELECT COUNT(DISTINCT sys_prin) FROM SYS_PRINS WHERE client = :client",
        nativeQuery = true)
    Page<SysPrin> findByIdClientDeduped(@Param("client") String client, Pageable pageable);
