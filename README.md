Helper   : The multi-part identifier "id.client" could not be bound.
2025-12-08T11:41:10.796-06:00 ERROR 29440 --- [client-sysprin-reader] [0.0-8083-exec-3] o.h.engine.jdbc.spi.SqlExceptionHelper   : The multi-part identifier "id.sysPrin" could not be bound.
2025-12-08T11:41:10.852-06:00 ERROR 29440 --- [client-sysprin-reader] [0.0-8083-exec-3] o.a.c.c.C.[.[.[/].[dispatcherServlet]    : Servlet.service() for servlet [dispatcherServlet] in context with path [] threw exception [Request processing failed: org.springframework.dao.InvalidDataAccessResourceUsageException: JDBC exception executing SQL [SELECT * FROM (
    SELECT
        sp.*,
        ROW_NUMBER() OVER (
            PARTITION BY sp.sys_prin
            ORDER BY sp.ACTIVE DESC, sp.sys_prin ASC
        ) as rn
    FROM SYS_PRINS sp -- Replace with your actual table name
    WHERE sp.client = ?
) deduped
WHERE deduped.rn = 1
 order by id.client asc, id.sysPrin asc offset ? rows fetch next ? rows only] [The multi-part identifier "id.client" could not be bound.] [n/a]; SQL [n/a]] with root cause

com.microsoft.sqlserver.jdbc.SQLServerException: The multi-part identifier "id.client" could not be bound.
        at com.microsoft.sqlserver.jdbc.SQLServerException.makeFromDatabaseError(SQLServerException.java:276) ~[mssql-jdbc-12.10.0.jre11.jar:na]




    @Query(value = """
        SELECT * FROM (
            SELECT 
                sp.*, 
                ROW_NUMBER() OVER (
                    PARTITION BY sp.sys_prin 
                    ORDER BY sp.ACTIVE DESC, sp.sys_prin ASC
                ) as rn
            FROM SYS_PRINS sp -- Replace with your actual table name
            WHERE sp.client = :client
        ) deduped
        WHERE deduped.rn = 1
        """,
            nativeQuery = true)
    Page<SysPrin> findByIdClientDeduped(@Param("client") String client, Pageable pageable);
