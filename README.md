2025-12-10T14:53:48.399-06:00 ERROR 33332 --- [client-sysprin-reader] [.0-8083-exec-10] o.a.c.c.C.[.[.[/].[dispatcherServlet]    : Servlet.service() for servlet [dispatcherServlet] in context with path [] threw exception [Request processing failed: org.springframework.data.mapping.PropertyReferenceException: No property 'report' found for type 'ClientReportOption'] with root cause

org.springframework.data.mapping.PropertyReferenceException: No property 'report' found for type 'ClientReportOption'
        at org.springframework.data.mapping.PropertyPath.<init>(PropertyPath.java:94) ~[spring-data-commons-3.5.0.jar:3.5.0]
        at org.springframework.data.mapping.PropertyPath.create(PropertyPath.java:455) ~[spring-data-commons-3.5.0.jar:3.5.0]
        at org.springframework.data.mapping.PropertyPath.create(PropertyPath.java:431) ~[spring-data-commons-3.5.0.jar:3.5.0]
        at org.springframework.data.mapping.PropertyPath.lambda$from$0(PropertyPath.java:384) ~[spring-data-commons-3.5.0.jar:3.5.0]
        at java.base/java.util.concurrent.ConcurrentMap.computeIfAbsent(ConcurrentMap.java:330) ~[na:na]
        at org.springframework.data.mapping.PropertyPath.from(PropertyPath.java:366) ~[spring-data-commons-3.5.0.jar:3.5.0]
        at org.springframework.data.mapping.PropertyPath.from(PropertyPath.java:344) ~[spring-data-commons-3.5.0.jar:3.5.0]
        at org.springframework.data.jpa.repository.query.QueryUtils.toJpaOrder(QueryUtils.java:727) ~[spring-data-jpa-3.5.0.jar:3.5.0]
        at org.springframework.data.jpa.repository.query.QueryUtils.toOrders(QueryUtils.java:680) ~[spring-data-jpa-3.5.0.jar:3.5.0]
        at org.springframework.data.jpa.repository.query.JpaQueryCreator.complete(JpaQueryCreator.java:195) ~[spring-data-jpa-3.5.0.jar:3.5.0]
        at org.springframework.data.jpa.repository.query.JpaQueryCreator.complete(JpaQueryCreator.java:142) ~[spring-data-jpa-3.5.0.jar:3.5.0]
        at org.springframework.data.jpa.repository.query.JpaQueryCreator.complete(JpaQueryCreator.java:1) ~[spring-data-jpa-3.5.0.jar:3.5.0]
        at org.springframework.data.repository.query.parser.AbstractQueryCreator.createQuery(AbstractQueryCreator.java:95) ~[spring-data-commons-3.5.0.jar:3.5.0]
        at org.springframework.data.jpa.repository.query.PartTreeJpaQuery$QueryPreparer.createQuery(PartTreeJpaQuery.java:239) ~[spring-data-jpa-3.5.0.jar:3.5.0]
        at org.springframework.data.jpa.repository.query.PartTreeJpaQuery.doCreateQuery(PartTreeJpaQuery.java:114) ~[spring-data-jpa-3.5.0.jar:3.5.0]
        at org.springframework.data.jpa.repository.query.AbstractJpaQuery.createQuery(AbstractJpaQuery.java:250) ~[spring-data-jpa-3.5.0.jar:3.5.0]
        at org.springframework.data.jpa.repository.query.JpaQueryExecution$PagedExecution.doExecute(JpaQueryExecution.java:203) ~[spring-data-jpa-3.5.0.jar:3.5.0]
        at org.springframework.data.jpa.repository.query.JpaQueryExecution.execute(JpaQueryExecution.java:93) ~[spring-data-jpa-3.5.0.jar:3.5.0]
        at org.springframework.data.jpa.repository.query.AbstractJpaQuery.doExecute(AbstractJpaQuery.java:159) ~[spring-data-jpa-3.5.0.jar:3.5.0]
        at org.springframework.data.jpa.repository.query.AbstractJpaQuery.execute(AbstractJpaQuery.java:147) ~[spring-data-jpa-3.5.0.jar:3.5.0]
        at org.springframework.data.repository.core.support.RepositoryMethodInvoker.doInvoke(RepositoryMethodInvoker.java:170) ~[spring-data-commons-3.5.0.jar:3.5.0]
        at org.springframework.data.repository.core.support.RepositoryMethodInvoker.invoke(RepositoryMethodInvoker.java:158) ~[spring-data-commons-3.5.0.jar:3.5.0]
        at org.springframework.data.repository.core.support.QueryExecutorMethodInterceptor.doInvoke(QueryExecutorMethodInterceptor.java:170) ~[spring-data-commons-3.5.0.jar:3.5.0]
        at org.springframework.data.repository.core.support.QueryExecutorMethodInterceptor.invoke(QueryExecutorMethodInterceptor.java:149) ~[spring-data-commons-3.5.0.jar:3.5.0]
        at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:184) ~[spring-aop-6.2.7.jar:6.2.7]
        at org.springframework.data.projection.DefaultMethodInvokingMethodInterceptor.invoke(DefaultMethodInvokingMethodInterceptor.java:69) ~[spring-data-commons-3.5.0.jar:3.5.0]
        at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:184) ~[spring-aop-6.2.7.jar:6.2.7]
        at org.springframework.transaction.interceptor.TransactionAspectSupport.invokeWithinTransaction(TransactionAspectSupport.java:380) ~[spring-tx-6.2


                Pageable pageable = PageRequest.of(page, size, Sort.by("report_id").ascending());
