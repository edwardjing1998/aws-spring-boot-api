2025-07-15T17:03:23.307-05:00  WARN 7608 --- [           main] ConfigServletWebServerApplicationContext : Exception encountered during context initialization - cancelling refresh attempt: org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'deletedCaseTransactionsDataGenerator': Invocation of init method failed
2025-07-15T17:03:23.308-05:00  INFO 7608 --- [           main] j.LocalContainerEntityManagerFactoryBean : Closing JPA EntityManagerFactory for persistence unit 'default'
2025-07-15T17:03:23.311-05:00  INFO 7608 --- [           main] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Shutdown initiated...
2025-07-15T17:03:23.314-05:00  INFO 7608 --- [           main] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Shutdown completed.
2025-07-15T17:03:23.315-05:00  INFO 7608 --- [           main] o.apache.catalina.core.StandardService   : Stopping service [Tomcat]
2025-07-15T17:03:23.327-05:00  INFO 7608 --- [           main] .s.b.a.l.ConditionEvaluationReportLogger :

Error starting ApplicationContext. To display the condition evaluation report re-run your application with 'debug' enabled.
2025-07-15T17:03:23.342-05:00 ERROR 7608 --- [           main] o.s.boot.SpringApplication               : Application run failed

org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'deletedCaseTransactionsDataGenerator': Invocation of init method failed
        at org.springframework.beans.factory.annotation.InitDestroyAnnotationBeanPostProcessor.postProcessBeforeInitialization(InitDestroyAnnotationBeanPostProcessor.java:222) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.applyBeanPostProcessorsBeforeInitialization(AbstractAutowireCapableBeanFactory.java:429) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.initializeBean(AbstractAutowireCapableBeanFactory.java:1818) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.doCreateBean(AbstractAutowireCapableBeanFactory.java:607) ~[spring-beans-6.2.7.jar:6.2.7]  
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBean(AbstractAutowireCapableBeanFactory.java:529) ~[spring-beans-6.2.7.jar:6.2.7]    
        at org.springframework.beans.factory.support.AbstractBeanFactory.lambda$doGetBean$0(AbstractBeanFactory.java:339) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.DefaultSingletonBeanRegistry.getSingleton(DefaultSingletonBeanRegistry.java:373) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.AbstractBeanFactory.doGetBean(AbstractBeanFactory.java:337) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.AbstractBeanFactory.getBean(AbstractBeanFactory.java:202) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.DefaultListableBeanFactory.instantiateSingleton(DefaultListableBeanFactory.java:1222) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.DefaultListableBeanFactory.preInstantiateSingleton(DefaultListableBeanFactory.java:1188) ~[spring-beans-6.2.7.jar:6.2.7]      
        at org.springframework.beans.factory.support.DefaultListableBeanFactory.preInstantiateSingletons(DefaultListableBeanFactory.java:1123) ~[spring-beans-6.2.7.jar:6.2.7]     
        at org.springframework.context.support.AbstractApplicationContext.finishBeanFactoryInitialization(AbstractApplicationContext.java:987) ~[spring-context-6.2.7.jar:6.2.7]   
        at org.springframework.context.support.AbstractApplicationContext.refresh(AbstractApplicationContext.java:627) ~[spring-context-6.2.7.jar:6.2.7]
        at org.springframework.boot.web.servlet.context.ServletWebServerApplicationContext.refresh(ServletWebServerApplicationContext.java:146) ~[spring-boot-3.5.0.jar:3.5.0]     
        at org.springframework.boot.SpringApplication.refresh(SpringApplication.java:753) ~[spring-boot-3.5.0.jar:3.5.0]
        at org.springframework.boot.SpringApplication.refreshContext(SpringApplication.java:439) ~[spring-boot-3.5.0.jar:3.5.0]
        at org.springframework.boot.SpringApplication.run(SpringApplication.java:318) ~[spring-boot-3.5.0.jar:3.5.0]
        at org.springframework.boot.SpringApplication.run(SpringApplication.java:1362) ~[spring-boot-3.5.0.jar:3.5.0]
        at org.springframework.boot.SpringApplication.run(SpringApplication.java:1351) ~[spring-boot-3.5.0.jar:3.5.0]
        at admin.RapidAdminApplication.main(RapidAdminApplication.java:12) ~[classes/:na]
Caused by: org.springframework.dao.DataIntegrityViolationException: could not execute statement [Unique index or primary key violation: "PUBLIC.PRIMARY_KEY_95 ON PUBLIC.DELETED_AC
COUNT_TRANS(CASE_NUMBER, TRANS_NO, DATE_TIME) VALUES ( /* key:2 */ CAST('CASE001     ' AS CHAR(12)), CAST(1001 AS NUMERIC(4)), TIMESTAMP '2025-07-15 17:03:23.296164')"; SQL statement:
insert into deleted_account_trans (account,action_id,action_reason,document_no,no_cards,operator_time,pi_id,postage_category_cd,sys_prin,uid,workstation_name_tx,case_number,date_t
ime,trans_no) values (?,?,?,?,?,?,?,?,?,?,?,?,?,?) [23505-232]] [insert into deleted_account_trans (account,action_id,action_reason,document_no,no_cards,operator_time,pi_id,postag
e_category_cd,sys_prin,uid,workstation_name_tx,case_number,date_time,trans_no) values (?,?,?,?,?,?,?,?,?,?,?,?,?,?)]; SQL [insert into deleted_account_trans (account,action_id,act
ion_reason,document_no,no_cards,operator_time,pi_id,postage_category_cd,sys_prin,uid,workstation_name_tx,case_number,date_time,trans_no) values (?,?,?,?,?,?,?,?,?,?,?,?,?,?)]; constraint [PUBLIC.PRIMARY_KEY_95]
        at org.springframework.orm.jpa.vendor.HibernateJpaDialect.convertHibernateAccessException(HibernateJpaDialect.java:294) ~[spring-orm-6.2.7.jar:6.2.7]
        at org.springframework.orm.jpa.vendor.HibernateJpaDialect.convertHibernateAccessException(HibernateJpaDialect.java:256) ~[spring-orm-6.2.7.jar:6.2.7]
        at org.springframework.orm.jpa.vendor.HibernateJpaDialect.translateExceptionIfPossible(HibernateJpaDialect.java:241) ~[spring-orm-6.2.7.jar:6.2.7]
        at org.springframework.orm.jpa.JpaTransactionManager.doCommit(JpaTransactionManager.java:566) ~[spring-orm-6.2.7.jar:6.2.7]
        at org.springframework.transaction.support.AbstractPlatformTransactionManager.processCommit(AbstractPlatformTransactionManager.java:795) ~[spring-tx-6.2.7.jar:6.2.7]      
        at org.springframework.transaction.support.AbstractPlatformTransactionManager.commit(AbstractPlatformTransactionManager.java:758) ~[spring-tx-6.2.7.jar:6.2.7]
        at org.springframework.transaction.interceptor.TransactionAspectSupport.commitTransactionAfterReturning(TransactionAspectSupport.java:698) ~[spring-tx-6.2.7.jar:6.2.7]    
        at org.springframework.transaction.interceptor.TransactionAspectSupport.invokeWithinTransaction(TransactionAspectSupport.java:416) ~[spring-tx-6.2.7.jar:6.2.7]
        at org.springframework.transaction.interceptor.TransactionInterceptor.invoke(TransactionInterceptor.java:119) ~[spring-tx-6.2.7.jar:6.2.7]
        at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:184) ~[spring-aop-6.2.7.jar:6.2.7]
        at org.springframework.dao.support.PersistenceExceptionTranslationInterceptor.invoke(PersistenceExceptionTranslationInterceptor.java:138) ~[spring-tx-6.2.7.jar:6.2.7]     
        at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:184) ~[spring-aop-6.2.7.jar:6.2.7]
        at org.springframework.data.jpa.repository.support.CrudMethodMetadataPostProcessor$CrudMethodMetadataPopulatingMethodInterceptor.invoke(CrudMethodMetadataPostProcessor.java:165) ~[spring-data-jpa-3.5.0.jar:3.5.0]
        at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:184) ~[spring-aop-6.2.7.jar:6.2.7]
        at org.springframework.aop.framework.JdkDynamicAopProxy.invoke(JdkDynamicAopProxy.java:223) ~[spring-aop-6.2.7.jar:6.2.7]
        at jdk.proxy2/jdk.proxy2.$Proxy175.save(Unknown Source) ~[na:na]
        at admin.config.data.DeletedCaseTransactionsDataGenerator.generateData(DeletedCaseTransactionsDataGenerator.java:101) ~[classes/:na]
        at java.base/jdk.internal.reflect.DirectMethodHandleAccessor.invoke(DirectMethodHandleAccessor.java:104) ~[na:na]
        at java.base/java.lang.reflect.Method.invoke(Method.java:565) ~[na:na]
        at org.springframework.beans.factory.annotation.InitDestroyAnnotationBeanPostProcessor$LifecycleMethod.invoke(InitDestroyAnnotationBeanPostProcessor.java:457) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.annotation.InitDestroyAnnotationBeanPostProcessor$LifecycleMetadata.invokeInitMethods(InitDestroyAnnotationBeanPostProcessor.java:401) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.annotation.InitDestroyAnnotationBeanPostProcessor.postProcessBeforeInitialization(InitDestroyAnnotationBeanPostProcessor.java:219) ~[spring-beans-6.2.7.jar:6.2.7]
        ... 20 common frames omitted
Caused by: org.hibernate.exception.ConstraintViolationException: could not execute statement [Unique index or primary key violation: "PUBLIC.PRIMARY_KEY_95 ON PUBLIC.DELETED_ACCOU
NT_TRANS(CASE_NUMBER, TRANS_NO, DATE_TIME) VALUES ( /* key:2 */ CAST('CASE001     ' AS CHAR(12)), CAST(1001 AS NUMERIC(4)), TIMESTAMP '2025-07-15 17:03:23.296164')"; SQL statement:
insert into deleted_account_trans (account,action_id,action_reason,document_no,no_cards,operator_time,pi_id,postage_category_cd,sys_prin,uid,workstation_name_tx,case_number,date_t
ime,trans_no) values (?,?,?,?,?,?,?,?,?,?,?,?,?,?) [23505-232]] [insert into deleted_account_trans (account,action_id,action_reason,document_no,no_cards,operator_time,pi_id,postage_category_cd,sys_prin,uid,workstation_name_tx,case_number,date_time,trans_no) values (?,?,?,?,?,?,?,?,?,?,?,?,?,?)]
        at org.hibernate.dialect.H2Dialect.lambda$buildSQLExceptionConversionDelegate$3(H2Dialect.java:759) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.exception.internal.StandardSQLExceptionConverter.convert(StandardSQLExceptionConverter.java:58) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.engine.jdbc.spi.SqlExceptionHelper.convert(SqlExceptionHelper.java:108) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.engine.jdbc.internal.ResultSetReturnImpl.executeUpdate(ResultSetReturnImpl.java:197) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.engine.jdbc.mutation.internal.AbstractMutationExecutor.performNonBatchedMutation(AbstractMutationExecutor.java:134) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.engine.jdbc.mutation.internal.MutationExecutorSingleNonBatched.performNonBatchedOperations(MutationExecutorSingleNonBatched.java:55) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.engine.jdbc.mutation.internal.AbstractMutationExecutor.execute(AbstractMutationExecutor.java:55) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.persister.entity.mutation.InsertCoordinatorStandard.doStaticInserts(InsertCoordinatorStandard.java:194) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]   
        at org.hibernate.persister.entity.mutation.InsertCoordinatorStandard.coordinateInsert(InsertCoordinatorStandard.java:132) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]  
        at org.hibernate.persister.entity.mutation.InsertCoordinatorStandard.insert(InsertCoordinatorStandard.java:104) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.action.internal.EntityInsertAction.execute(EntityInsertAction.java:110) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.engine.spi.ActionQueue.executeActions(ActionQueue.java:644) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.engine.spi.ActionQueue.executeActions(ActionQueue.java:511) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.event.internal.AbstractFlushingEventListener.performExecutions(AbstractFlushingEventListener.java:414) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]    
        at org.hibernate.event.internal.DefaultFlushEventListener.onFlush(DefaultFlushEventListener.java:41) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.event.service.internal.EventListenerGroupImpl.fireEventOnEachListener(EventListenerGroupImpl.java:127) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]    
        at org.hibernate.internal.SessionImpl.doFlush(SessionImpl.java:1429) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.internal.SessionImpl.managedFlush(SessionImpl.java:491) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.internal.SessionImpl.flushBeforeTransactionCompletion(SessionImpl.java:2354) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.internal.SessionImpl.beforeTransactionCompletion(SessionImpl.java:1978) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.engine.jdbc.internal.JdbcCoordinatorImpl.beforeTransactionCompletion(JdbcCoordinatorImpl.java:439) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]        
        at org.hibernate.resource.transaction.backend.jdbc.internal.JdbcResourceLocalTransactionCoordinatorImpl.beforeCompletionCallback(JdbcResourceLocalTransactionCoordinatorImpl.java:169) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.resource.transaction.backend.jdbc.internal.JdbcResourceLocalTransactionCoordinatorImpl$TransactionDriverControlImpl.commit(JdbcResourceLocalTransactionCoordinatorImpl.java:267) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.engine.transaction.internal.TransactionImpl.commit(TransactionImpl.java:101) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.springframework.orm.jpa.JpaTransactionManager.doCommit(JpaTransactionManager.java:562) ~[spring-orm-6.2.7.jar:6.2.7]
        ... 38 common frames omitted
Caused by: org.h2.jdbc.JdbcSQLIntegrityConstraintViolationException: Unique index or primary key violation: "PUBLIC.PRIMARY_KEY_95 ON PUBLIC.DELETED_ACCOUNT_TRANS(CASE_NUMBER, TRANS_NO, DATE_TIME) VALUES ( /* key:2 */ CAST('CASE001     ' AS CHAR(12)), CAST(1001 AS NUMERIC(4)), TIMESTAMP '2025-07-15 17:03:23.296164')"; SQL statement:
insert into deleted_account_trans (account,action_id,action_reason,document_no,no_cards,operator_time,pi_id,postage_category_cd,sys_prin,uid,workstation_name_tx,case_number,date_time,trans_no) values (?,?,?,?,?,?,?,?,?,?,?,?,?,?) [23505-232]
        at org.h2.message.DbException.getJdbcSQLException(DbException.java:520) ~[h2-2.3.232.jar:2.3.232]
        at org.h2.message.DbException.getJdbcSQLException(DbException.java:489) ~[h2-2.3.232.jar:2.3.232]
        at org.h2.message.DbException.get(DbException.java:223) ~[h2-2.3.232.jar:2.3.232]
        at org.h2.message.DbException.get(DbException.java:199) ~[h2-2.3.232.jar:2.3.232]
        at org.h2.index.Index.getDuplicateKeyException(Index.java:523) ~[h2-2.3.232.jar:2.3.232]
        at org.h2.mvstore.db.MVSecondaryIndex.checkUnique(MVSecondaryIndex.java:223) ~[h2-2.3.232.jar:2.3.232]
        at org.h2.mvstore.db.MVSecondaryIndex.add(MVSecondaryIndex.java:184) ~[h2-2.3.232.jar:2.3.232]
        at org.h2.mvstore.db.MVTable.addRow(MVTable.java:517) ~[h2-2.3.232.jar:2.3.232]
        at org.h2.command.dml.Insert.insertRows(Insert.java:174) ~[h2-2.3.232.jar:2.3.232]
        at org.h2.command.dml.Insert.update(Insert.java:135) ~[h2-2.3.232.jar:2.3.232]
        at org.h2.command.dml.DataChangeStatement.update(DataChangeStatement.java:74) ~[h2-2.3.232.jar:2.3.232]
        at org.h2.command.CommandContainer.update(CommandContainer.java:139) ~[h2-2.3.232.jar:2.3.232]
        at org.h2.command.Command.executeUpdate(Command.java:304) ~[h2-2.3.232.jar:2.3.232]
        at org.h2.command.Command.executeUpdate(Command.java:248) ~[h2-2.3.232.jar:2.3.232]
        at org.h2.jdbc.JdbcPreparedStatement.executeUpdateInternal(JdbcPreparedStatement.java:213) ~[h2-2.3.232.jar:2.3.232]
        at org.h2.jdbc.JdbcPreparedStatement.executeUpdate(JdbcPreparedStatement.java:172) ~[h2-2.3.232.jar:2.3.232]
        at com.zaxxer.hikari.pool.ProxyPreparedStatement.executeUpdate(ProxyPreparedStatement.java:61) ~[HikariCP-6.3.0.jar:na]
        at com.zaxxer.hikari.pool.HikariProxyPreparedStatement.executeUpdate(HikariProxyPreparedStatement.java) ~[HikariCP-6.3.0.jar:na]
        at org.hibernate.engine.jdbc.internal.ResultSetReturnImpl.executeUpdate(ResultSetReturnImpl.java:194) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        ... 59 common frames omitted

[INFO] ------------------------------------------------------------------------
[INFO] BUILD FAILURE
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  22.162 s
[INFO] Finished at: 2025-07-15T17:03:23-05:00
[INFO] ------------------------------------------------------------------------
[ERROR] Failed to execute goal org.springframework.boot:spring-boot-maven-plugin:3.4.2:run (default-cli) on project admin: Process terminated with exit code: 1 -> [Help 1]        
[ERROR]
[ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
[ERROR] Re-run Maven using the -X switch to enable full debug logging.
[ERROR]
[ERROR] For more information about the errors and possible solutions, please read the following articles:
