2025-12-08T12:05:14.128-06:00  INFO 34644 --- [client-sysprin-reader] [0.0-8083-exec-4] .s.w.SysPrinPaginationByClientController : Paged SysPrin request: client=0042, page=1, size=10
Hibernate: SELECT * FROM (
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
 order by client asc, sys_prin asc offset ? rows fetch next ? rows only
Hibernate: select count(1) FROM SYS_PRINS sp -- Replace with your actual table name
    WHERE sp.client = ?
) deduped
WHERE deduped.rn = 1

2025-12-08T12:05:14.990-06:00  WARN 34644 --- [client-sysprin-reader] [0.0-8083-exec-4] o.h.engine.jdbc.spi.SqlExceptionHelper   : SQL Error: 102, SQLState: S0001
2025-12-08T12:05:14.990-06:00 ERROR 34644 --- [client-sysprin-reader] [0.0-8083-exec-4] o.h.engine.jdbc.spi.SqlExceptionHelper   : Incorrect syntax near ')'.
2025-12-08T12:05:15.006-06:00 ERROR 34644 --- [client-sysprin-reader] [0.0-8083-exec-4] o.a.c.c.C.[.[.[/].[dispatcherServlet]    : Servlet.service() for servlet [dispatcherServlet] in context with path [] threw exception [Request processing failed: org.springframework.dao.InvalidDataAccessResourceUsageException: JDBC exception executing SQL [select count(1) FROM SYS_PRINS sp -- Replace with your actual table name
    WHERE sp.client = ?
) deduped
WHERE deduped.rn = 1
] [Incorrect syntax near ')'.] [n/a]; SQL [n/a]] with root cause

com.microsoft.sqlserver.jdbc.SQLServerException: Incorrect syntax near ')'.
        at com.microsoft.sqlserver.jdbc.SQLServerException.makeFromDatabaseError(SQLServerException.java:276) ~[mssql-jdbc-12.10.0.jre11.jar:na]
        at com.microsoft.sqlserver.jdbc.SQLServerStatement.getNextResult(SQLServerStatement.java:1787) ~[mssql-jdbc-12.10.0.jre11.jar:na]
        at com.microsoft.sqlserver.jdbc.SQLServerPreparedStatement.doExecutePreparedStatement(SQLServerPreparedStatement.java:688) ~[mssql-jdbc-12.10.0.jre11.jar:na]
        at com.microsoft.sqlserver.j
