tcherServlet] in context with path [] threw exception [Request processing failed: org.springframework.dao.DataIntegrityViolationException: JDBC exception executing SQL [UPDATE clients
SET
  active               = COALESCE(?, active),
  addr                 = COALESCE(?, addr),
  amex_issued          = COALESCE(?, amex_issued),
  billing_sp           = COALESCE(?, billing_sp),
  chlookup_type        = COALESCE(?, chlookup_type),
  city                 = COALESCE(?, city),
  contact              = COALESCE(?, contact),
  exclude_from_report  = COALESCE(?, exclude_from_report),
  fax_number           = COALESCE(?, fax_number),
  name                 = COALESCE(?, name),
  phone                = COALESCE(?, phone),
  positive_reports     = COALESCE(?, positive_reports),
  report_break_flag    = COALESCE(?, report_break_flag),
  state                = COALESCE(?, state),
  sub_client_ind       = COALESCE(?, sub_client_ind),
  sub_client_xref      = COALESCE(?, sub_client_xref),
  zip                  = COALESCE(?, zip)
WHERE client = ?
] [String or binary data would be truncated.] [n/a]; SQL [n/a]] with root cause

com.microsoft.sqlserver.jdbc.SQLServerException: String or binary data would be truncated.
        at com.microsoft.sqlserver.jdbc.SQLServerException.makeFromDatabaseError(SQLServerException.java:276) ~[mssql-jdbc-12.10.0.jre11.jar:na]
        at com.microsoft.sqlserver.jdbc.SQLServerStatement.getNextResult(SQLServerStatement.java:1787) ~[mssql-jdbc-12.10.0.jre11.jar:na]
        at com.microsoft.sqlserver.jdbc.SQLServerPreparedStatement.doExecutePreparedStatement(SQLServerPreparedStatement.java:688) ~[mssql-jdbc-12.10.0.jre11.jar:na]
        at com.microsoft.sqlserver.jdbc.SQLServerPreparedStatement$PrepStmtExecCmd.doExecute(SQLServerPreparedStatement.java:607) ~[mssql-jdbc-12.10.0.jre11.jar:na]
        at com.microsoft.sqlserver.jdbc.TDSCommand.execute(IOBuffer.java:7745) ~[mssql-jdbc-12.10.0.jre11.jar:na]
        at com.microsoft.sqlserver.jdbc.SQLServerConnection.executeCommand(SQLServerConnection.java:4700) ~[mssql-jdbc-12.10.0.jre11.jar:na]
        at com.microsoft.sqlserver.jdbc.SQLServerStatement.executeCommand(SQLServerStatement.java:321) ~[mssql-jdbc-12.10.0.jre11.jar:na]
        at com.microsoft.sqlserver.jdbc.SQLServerStatement.executeStatement(SQLServerStatement.java:253) ~[mssql-jdbc-12.10.0.jre11.jar:na]
        at com.microsoft.sqlserver.jdbc.SQLServerPreparedStatement.executeUpdate(SQLServerPreparedStatement.java:549) ~[mssql-jdbc-12.10.0.jre11.jar:na]
        at com.zaxxer.hikari.pool.ProxyPreparedStatement.executeUpdate(ProxyPreparedStatement.java:61) ~[HikariCP-6.3.0.jar:na]
        at com.zaxxer.hikari.pool.HikariProxyPreparedStatement.executeUpdate(HikariProxyPreparedStatement.java) ~[HikariCP-6.3.0.jar:na]
        at org.hibernate.sql.exec.internal.StandardJdbcMutationExecutor.execute(StandardJdbcMutationExecutor.java:90) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.query.sql.internal.NativeNonSelectQueryPlanImpl.executeUpdate(NativeNonSelectQueryPlanImpl.java:76) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.query.sql.internal.NativeQueryImpl.doExecuteUpdate(NativeQueryImpl.java:973) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.query.spi.AbstractQuery.executeUpdate(AbstractQuery.java:651) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.springframework.data.jpa.repository.query.JpaQueryExecution$ModifyingExecution.doExecute(JpaQueryExecution.java:267) ~[spring-data-jpa-3.5.0.jar:3.5.0]
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
        at org.springframework.transaction.interceptor.TransactionAspectSupport.invokeWithinTransaction(TransactionAspectSupport.java:380) ~[spring-tx-6.2.7.jar:6.2.7]
        at org.springframework.transaction.interceptor.TransactionInterceptor.invoke(TransactionInterceptor.java:119) ~[spring-tx-6.2.7.jar:6.2.7]
        at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:184) ~[spring-aop-6.2.7.jar:6.2.7]
        at org.springframework.dao.support.PersistenceExceptionTranslationInterceptor.invoke(PersistenceExceptionTranslationInterceptor.java:138) ~[spring-tx-6.2.7.jar:6.2.7]
        at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:184) ~[spring-aop-6.2.7.jar:6.2.7]
        at org.springframework.data.jpa.repository.support.CrudMethodMetadataPostProcessor$CrudMethodMetadataPopulatingMethodInterceptor.invoke(CrudMethodMetadataPostProcessor.java:136) ~[spring-data-jpa-3.5.0.jar:3.5.0]
        at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:184) ~[spring-aop-6.2.7.jar:6.2.7]
        at org.springframework.aop.framework.JdkDynamicAopProxy.invoke(JdkDynamicAopProxy.java:223) ~[spring-aop-6.2.7.jar:6.2.7]
        at jdk.proxy2/jdk.proxy2.$Proxy161.bulkUpdateByClient(Unknown Source) ~[na:na]
        at rapid.service.client.ClientService.updateClient(ClientService.java:67) ~[common-services-0.0.1-SNAPSHOT.jar:na]
        at java.base/jdk.internal.reflect.DirectMethodHandleAccessor.invoke(DirectMethodHandleAccessor.java:104) ~[na:na]
        at java.base/java.lang.reflect.Method.invoke(Method.java:565) ~[na:na]
        at org.springframework.aop.support.AopUtils.invokeJoinpointUsingReflection(AopUtils.java:359) ~[spring-aop-6.2.7.jar:6.2.7]
        at org.springframework.aop.framework.ReflectiveMethodInvocation.invokeJoinpoint(ReflectiveMethodInvocation.java:196) ~[spring-aop-6.2.7.jar:6.2.7]
        at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:163) ~[spring-aop-6.2.7.jar:6.2.7]
        at org.springframework.transaction.interceptor.TransactionAspectSupport.invokeWithinTransaction(TransactionAspectSupport.java:380) ~[spring-tx-6.2.7.jar:6.2.7]
        at org.springframework.transaction.interceptor.TransactionInterceptor.invoke(TransactionInterceptor.java:119) ~[spring-tx-6.2.7.jar:6.2.7]
        at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:184) ~[spring-aop-6.2.7.jar:6.2.7]
        at org.springframework.aop.framework.CglibAopProxy$DynamicAdvisedInterceptor.intercept(CglibAopProxy.java:728) ~[spring-aop-6.2.7.jar:6.2.7]
        at rapid.service.client.ClientService$$SpringCGLIB$$0.updateClient(<generated>) ~[common-services-0.0.1-SNAPSHOT.jar:na]
        at rapid.client.web.ClientWriterController.updateClient(ClientWriterController.java:37) ~[classes/:na]
        at java.base/jdk.internal.reflect.DirectMethodHandleAccessor.invoke(DirectMethodHandleAccessor.java:104) ~[na:na]
        at java.base/java.lang.reflect.Method.invoke(Method.java:565) ~[na:na]
        at org.springframework.aop.support.AopUtils.invokeJoinpointUsingReflection(AopUtils.java:359) ~[spring-aop-6.2.7.jar:6.2.7]
        at org.springframework.aop.framework.ReflectiveMethodInvocation.invokeJoinpoint(ReflectiveMethodInvocation.java:196) ~[spring-aop-6.2.7.jar:6.2.7]
        at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:163) ~[spring-aop-6.2.7.jar:6.2.7]
        at org.springframework.validation.beanvalidation.MethodValidationInterceptor.invoke(MethodValidationInterceptor.java:174) ~[spring-context-6.2.7.jar:6.2.7]
        at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:184) ~[spring-aop-6.2.7.jar:6.2.7]
        at org.springframework.aop.framework.CglibAopProxy$DynamicAdvisedInterceptor.intercept(CglibAopProxy.java:728) ~[spring-aop-6.2.7.jar:6.2.7]
        at rapid.client.web.ClientWriterController$$SpringCGLIB$$0.updateClient(<generated>) ~[classes/:na]
        at java.base/jdk.internal.reflect.DirectMethodHandleAccessor.invoke(DirectMethodHandleAccessor.java:104) ~[na:na]
        at java.base/java.lang.reflect.Method.invoke(Method.java:565) ~[na:na]
        at org.springframework.web.method.support.InvocableHandlerMethod.doInvoke(InvocableHandlerMethod.java:258) ~[spring-web-6.2.7.jar:6.2.7]
        at org.springframework.web.method.support.InvocableHandlerMethod.invokeForRequest(InvocableHandlerMethod.java:191) ~[spring-web-6.2.7.jar:6.2.7]
        at org.spri
