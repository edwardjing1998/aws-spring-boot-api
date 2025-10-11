2025-10-11T13:19:41.654-05:00  INFO 32968 --- [client-sysprin-reader] [0.0-8083-exec-1] o.s.web.servlet.DispatcherServlet        : Initializing Servlet 'dispatcherServlet'
2025-10-11T13:19:41.660-05:00  INFO 32968 --- [client-sysprin-reader] [0.0-8083-exec-1] o.s.web.servlet.DispatcherServlet        : Completed initialization in 5 ms
2025-10-11T13:19:41.991-05:00  INFO 32968 --- [client-sysprin-reader] [0.0-8083-exec-1] rapid.client.web.ClientController        : request client details for page 0 size 20
2025-10-11T13:19:42.140-05:00  WARN 32968 --- [client-sysprin-reader] [0.0-8083-exec-1] org.hibernate.orm.query                  : HHH90003004: firstResult/maxResults specified with collection fetch; applying in memory
2025-10-11T13:19:42.331-05:00 ERROR 32968 --- [client-sysprin-reader] [0.0-8083-exec-1] o.a.c.c.C.[.[.[/].[dispatcherServlet]    : Servlet.service() for servlet [dispatcherServlet] in context with path [] threw exception [Request processing failed: org.springframework.dao.InvalidDataAccessApiUsageException: org.hibernate.loader.MultipleBagFetchException: cannot simultaneously fetch multiple bags: [rapid.model.client.Client.clientEmails, rapid.model.client.Client.reportOptions]] with root cause

org.hibernate.loader.MultipleBagFetchException: cannot simultaneously fetch multiple bags: [rapid.model.client.Client.clientEmails, rapid.model.client.Client.reportOptions]
        at org.hibernate.query.sqm.sql.BaseSqmToSqlAstConverter.createFetch(BaseSqmToSqlAstConverter.java:8572) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.query.sqm.sql.BaseSqmToSqlAstConverter.visitFetches(BaseSqmToSqlAstConverter.java:8618) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.sql.results.graph.AbstractFetchParent.afterInitialize(AbstractFetchParent.java:42) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.sql.results.graph.entity.AbstractEntityResultGraphNode.afterInitialize(AbstractEntityResultGraphNode.java:74) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.persister.entity.AbstractEntityPersister.createDomainResult(AbstractEntityPersister.java:1421) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.query.sqm.sql.internal.AbstractSqmPathInterpretation.createDomainResult(AbstractSqmPathInterpretation.java:55) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.query.sqm.sql.BaseSqmToSqlAstConverter.lambda$visitSelection$18(BaseSqmToSqlAstConverter.java:2305) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at java.base/java.util.Collections$SingletonList.forEach(Collections.java:5297) ~[na:na]
        at org.hibernate.query.sqm.sql.BaseSqmToSqlAstConverter.visitSelection(BaseSqmToSqlAstConverter.java:2300) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.query.sqm.sql.BaseSqmToSqlAstConverter.visitSelectClause(BaseSqmToSqlAstConverter.java:2218) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.query.sqm.sql.BaseSqmToSqlAstConverter.visitQuerySpec(BaseSqmToSqlAstConverter.java:2089) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.query.sqm.sql.BaseSqmToSqlAstConverter.visitQuerySpec(BaseSqmToSqlAstConverter.java:454) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.query.sqm.tree.select.SqmQuerySpec.accept(SqmQuerySpec.java:124) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.query.sqm.spi.BaseSemanticQueryWalker.visitQueryPart(BaseSemanticQueryWalker.java:245) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.query.sqm.sql.BaseSqmToSqlAstConverter.visitQueryPart(BaseSqmToSqlAstConverter.java:1943) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.query.sqm.sql.BaseSqmToSqlAstConverter.visitSelectStatement(BaseSqmToSqlAstConverter.java:1629) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.query.sqm.sql.BaseSqmToSqlAstConverter.visitSelectStatement(BaseSqmToSqlAstConverter.java:454) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.query.sqm.tree.select.SqmSelectStatement.accept(SqmSelectStatement.java:238) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.query.sqm.sql.BaseSqmToSqlAstConverter.translate(BaseSqmToSqlAstConverter.java:800) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.query.sqm.internal.ConcreteSqmSelectQueryPlan.buildCacheableSqmInterpretation(ConcreteSqmSelectQueryPlan.java:482) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
