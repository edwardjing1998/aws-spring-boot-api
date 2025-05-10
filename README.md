Caused by: org.springframework.dao.DataIntegrityViolationException: could not execute statement [NULL not allowed for column "REPORT_ID"; SQL statement:
insert into admin_query_list (access_level,db_driver_type,default_file_nm,email_event_id,email_from_address,file_ext,file_header_ind,file_transfer_type,input_data_fields,input_file
_key_length,input_file_key_start_pos,input_file_tx,is_active,is_visible,num_sheets,query,query_name,report_by_client_flag,report_db,report_db_ip_and_port,report_db_passwrd,report_d
b_server,report_db_userid,rerun_client_id,rerun_date_range_end,rerun_date_range_start,tab_delimited_flag,report_id) values (?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,de
fault) [23502-232]] [insert into admin_query_list (access_level,db_driver_type,default_file_nm,email_event_id,email_from_address,file_ext,file_header_ind,file_transfer_type,input_d
ata_fields,input_file_key_length,input_file_key_start_pos,input_file_tx,is_active,is_visible,num_sheets,query,query_name,report_by_client_flag,report_db,report_db_ip_and_port,repor
t_db_passwrd,report_db_server,report_db_userid,rerun_client_id,rerun_date_range_end,rerun_date_range_start,tab_delimited_flag,report_id) values (?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?
,?,?,?,?,?,?,?,?,?,default)]; SQL [insert into admin_query_list (access_level,db_driver_type,default_file_nm,email_event_id,email_from_address,file_ext,file_header_ind,file_transfe
r_type,input_data_fields,input_file_key_length,input_file_key_start_pos,input_file_tx,is_active,is_visible,num_sheets,query,query_name,report_by_client_flag,report_db,report_db_ip_
and_port,report_db_passwrd,report_db_server,report_db_userid,rerun_client_id,rerun_date_range_end,rerun_date_range_start,tab_delimited_flag,report_id) values (?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,default)]; constraint [null]
        at org.springframework.orm.jpa.vendor.HibernateJpaDialect.convertHibernateAccessException(HibernateJpaDialect.java:290) ~[spring-orm-6.2.3.jar:6.2.3]
        at org.springframework.orm.jpa.vendor.HibernateJpaDialect.translateExceptionIfPossible(HibernateJpaDialect.java:241) ~[spring-orm-6.2.3.jar:6.2.3]
        at org.springframework.orm.jpa.AbstractEntityManagerFactoryBean.translateExceptionIfPossible(AbstractEntityManagerFactoryBean.java:560) ~[spring-orm-6.2.3.jar:6.2.3]       
        at org.springframework.dao.support.ChainedPersistenceExceptionTranslator.translateExceptionIfPossible(ChainedPersistenceExceptionTranslator.java:61) ~[spring-tx-6.2.3.jar:6.2.3]
        at org.springframework.dao.support.DataAccessUtils.translateIfNecessary(DataAccessUtils.java:343) ~[spring-tx-6.2.3.jar:6.2.3]
        at org.springframework.dao.support.PersistenceExceptionTranslationInterceptor.invoke(PersistenceExceptionTranslationInterceptor.java:160) ~[spring-tx-6.2.3.jar:6.2.3]      
        at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:184) ~[spring-aop-6.2.3.jar:6.2.3]
        at org.springframework.data.jpa.repository.support.CrudMethodMetadataPostProcessor$CrudMethodMetadataPopulatingMethodInterceptor.invoke(CrudMethodMetadataPostProcessor.java:165) ~[spring-data-jpa-3.4.3.jar:3.4.3]
        at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:184) ~[spring-aop-6.2.3.jar:6.2.3]
        at org.springframework.aop.framework.JdkDynamicAopProxy.invoke(JdkDynamicAopProxy.java:223) ~[spring-aop-6.2.3.jar:6.2.3]
        at jdk.proxy2/jdk.proxy2.$Proxy151.saveAll(Unknown Source) ~[na:na]
        at admin.config.data.ClientDataGenerator.generateClients(ClientDataGenerator.java:84) ~[classes/:na]
        at admin.config.data.DataGenerationOrchestrator.runDataGeneration(DataGenerationOrchestrator.java:29) ~[classes/:na]
        at java.base/jdk.internal.reflect.DirectMethodHandleAccessor.invoke(DirectMethodHandleAccessor.java:104) ~[na:na]
        at java.base/java.lang.reflect.Method.invoke(Method.java:565) ~[na:na]
        at org.springframework.beans.factory.annotation.InitDestroyAnnotationBeanPostProcessor$LifecycleMethod.invoke(InitDestroyAnnotationBeanPostProcessor.java:457) ~[spring-beans-6.2.3.jar:6.2.3]
        at org.springframework.beans.factory.annotation.InitDestroyAnnotationBeanPostProcessor$LifecycleMetadata.invokeInitMethods(InitDestroyAnnotationBeanPostProcessor.java:401) ~[spring-beans-6.2.3.jar:6.2.3]
        at org.springframework.beans.factory.annotation.InitDestroyAnnotationBeanPostProcessor.postProcessBeforeInitialization(InitDestroyAnnotationBeanPostProcessor.java:219) ~[spring-beans-6.2.3.jar:6.2.3]
        ... 20 common frames omitted
Caused by: org.hibernate.exception.ConstraintViolationException: could not execute statement [NULL not allowed for column "REPORT_ID"; SQL statement:
insert into admin_query_list (access_level,db_driver_type,default_file_nm,email_event_id,email_from_address,file_ext,file_header_ind,file_transfer_type,input_data_fields,input_file
_key_length,input_file_key_start_pos,input_file_tx,is_active,is_visible,num_sheets,query,query_name,report_by_client_flag,report_db,report_db_ip_and_port,report_db_passwrd,report_d
b_server,report_db_userid,rerun_client_id,rerun_date_range_end,rerun_date_range_start,tab_delimited_flag,report_id) values (?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,de
fault) [23502-232]] [insert into admin_query_list (access_level,db_driver_type,default_file_nm,email_event_id,email_from_address,file_ext,file_header_ind,file_transfer_type,input_d
ata_fields,input_file_key_length,input_file_key_start_pos,input_file_tx,is_active,is_visible,num_sheets,query,query_name,report_by_client_flag,report_db,report_db_ip_and_port,repor
t_db_passwrd,report_db_server,report_db_userid,rerun_client_id,rerun_date_range_end,rerun_date_range_start,tab_delimited_flag,report_id) values (?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,default)]
        at org.hibernate.exception.internal.SQLExceptionTypeDelegate.convert(SQLExceptionTypeDelegate.java:62) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]
        at org.hibernate.exception.internal.StandardSQLExceptionConverter.convert(StandardSQLExceptionConverter.java:58) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]
        at org.hibernate.engine.jdbc.spi.SqlExceptionHelper.convert(SqlExceptionHelper.java:108) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]
        at org.hibernate.engine.jdbc.internal.ResultSetReturnImpl.executeUpdate(ResultSetReturnImpl.java:197) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]
        at org.hibernate.id.insert.GetGeneratedKeysDelegate.performMutation(GetGeneratedKeysDelegate.java:116) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]
        at org.hibernate.engine.jdbc.mutation.internal.MutationExecutorSingleNonBatched.performNonBatchedOperations(MutationExecutorSingleNonBatched.java:47) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]
        at org.hibernate.engine.jdbc.mutation.internal.AbstractMutationExecutor.execute(AbstractMutationExecutor.java:55) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]
        at org.hibernate.persister.entity.mutation.InsertCoordinatorStandard.doStaticInserts(InsertCoordinatorStandard.java:194) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]      
        at org.hibernate.persister.entity.mutation.InsertCoordinatorStandard.coordinateInsert(InsertCoordinatorStandard.java:132) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]     
        at org.hibernate.persister.entity.mutation.InsertCoordinatorStandard.insert(InsertCoordinatorStandard.java:95) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]
        at org.hibernate.action.internal.EntityIdentityInsertAction.execute(EntityIdentityInsertAction.java:85) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]
        at org.hibernate.engine.spi.ActionQueue.execute(ActionQueue.java:682) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]
        at org.hibernate.engine.spi.ActionQueue.addResolvedEntityInsertAction(ActionQueue.java:293) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]
        at org.hibernate.engine.spi.ActionQueue.addInsertAction(ActionQueue.java:274) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]
        at org.hibernate.engine.spi.ActionQueue.addAction(ActionQueue.java:324) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]
        at org.hibernate.event.internal.AbstractSaveEventListener.addInsertAction(AbstractSaveEventListener.java:393) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]
        at org.hibernate.event.internal.AbstractSaveEventListener.performSaveOrReplicate(AbstractSaveEventListener.java:307) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]
        at org.hibernate.event.internal.AbstractSaveEventListener.performSave(AbstractSaveEventListener.java:223) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]
        at org.hibernate.event.internal.AbstractSaveEventListener.saveWithGeneratedId(AbstractSaveEventListener.java:136) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]
        at org.hibernate.event.internal.DefaultPersistEventListener.entityIsTransient(DefaultPersistEventListener.java:177) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]
        at org.hibernate.event.internal.DefaultPersistEventListener.persist(DefaultPersistEventListener.java:95) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]
        at org.hibernate.event.internal.DefaultPersistEventListener.onPersist(DefaultPersistEventListener.java:79) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]
        at org.hibernate.event.internal.DefaultPersistEventListener.onPersist(DefaultPersistEventListener.java:55) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]
        at org.hibernate.event.service.internal.EventListenerGroupImpl.fireEventOnEachListener(EventListenerGroupImpl.java:127) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]       
        at org.hibernate.internal.SessionImpl.firePersist(SessionImpl.java:761) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]
        at org.hibernate.internal.SessionImpl.persist(SessionImpl.java:745) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]
        at java.base/jdk.internal.reflect.DirectMethodHandleAccessor.invoke(DirectMethodHandleAccessor.java:104) ~[na:na]
        at java.base/java.lang.reflect.Method.invoke(Method.java:565) ~[na:na]
        at org.springframework.orm.jpa.SharedEntityManagerCreator$SharedEntityManagerInvocationHandler.invoke(SharedEntityManagerCreator.java:320) ~[spring-orm-6.2.3.jar:6.2.3]    
        at jdk.proxy2/jdk.proxy2.$Proxy138.persist(Unknown Source) ~[na:na]
        at org.springframework.data.jpa.repository.support.SimpleJpaRepository.save(SimpleJpaRepository.java:636) ~[spring-data-jpa-3.4.3.jar:3.4.3]
        at org.springframework.data.jpa.repository.support.SimpleJpaRepository.saveAll(SimpleJpaRepository.java:662) ~[spring-data-jpa-3.4.3.jar:3.4.3]
        at java.base/jdk.internal.reflect.DirectMethodHandleAccessor.invoke(DirectMethodHandleAccessor.java:104) ~[na:na]
        at java.base/java.lang.reflect.Method.invoke(Method.java:565) ~[na:na]
        at org.springframework.aop.support.AopUtils.invokeJoinpointUsingReflection(AopUtils.java:359) ~[spring-aop-6.2.3.jar:6.2.3]
        at org.springframework.data.repository.core.support.RepositoryMethodInvoker$RepositoryFragmentMethodInvoker.lambda$new$0(RepositoryMethodInvoker.java:277) ~[spring-data-commons-3.4.3.jar:3.4.3]
        at org.springframework.data.repository.core.support.RepositoryMethodInvoker.doInvoke(RepositoryMethodInvoker.java:170) ~[spring-data-commons-3.4.3.jar:3.4.3]
        at org.springframework.data.repository.core.support.RepositoryMethodInvoker.invoke(RepositoryMethodInvoker.java:158) ~[spring-data-commons-3.4.3.jar:3.4.3]
        at org.springframework.data.repository.core.support.RepositoryComposition$RepositoryFragments.invoke(RepositoryComposition.java:515) ~[spring-data-commons-3.4.3.jar:3.4.3] 
        at org.springframework.data.repository.core.support.RepositoryComposition.invoke(RepositoryComposition.java:284) ~[spring-data-commons-3.4.3.jar:3.4.3]
        at org.springframework.data.repository.core.support.RepositoryFactorySupport$ImplementationMethodExecutionInterceptor.invoke(RepositoryFactorySupport.java:752) ~[spring-data-commons-3.4.3.jar:3.4.3]
        at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:184) ~[spring-aop-6.2.3.jar:6.2.3]
        at org.springframework.data.repository.core.support.QueryExecutorMethodInterceptor.doInvoke(QueryExecutorMethodInterceptor.java:174) ~[spring-data-commons-3.4.3.jar:3.4.3] 
        at org.springframework.data.repository.core.support.QueryExecutorMethodInterceptor.invoke(QueryExecutorMethodInterceptor.java:149) ~[spring-data-commons-3.4.3.jar:3.4.3]   
        at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:184) ~[spring-aop-6.2.3.jar:6.2.3]
        at org.springframework.data.projection.DefaultMethodInvokingMethodInterceptor.invoke(DefaultMethodInvokingMethodInterceptor.java:69) ~[spring-data-commons-3.4.3.jar:3.4.3] 
        at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:184) ~[spring-aop-6.2.3.jar:6.2.3]
        at org.springframework.transaction.interceptor.TransactionAspectSupport.invokeWithinTransaction(TransactionAspectSupport.java:380) ~[spring-tx-6.2.3.jar:6.2.3]
        at org.springframework.transaction.interceptor.TransactionInterceptor.invoke(TransactionInterceptor.java:119) ~[spring-tx-6.2.3.jar:6.2.3]
        at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:184) ~[spring-aop-6.2.3.jar:6.2.3]
        at org.springframework.dao.support.PersistenceExceptionTranslationInterceptor.invoke(PersistenceExceptionTranslationInterceptor.java:138) ~[spring-tx-6.2.3.jar:6.2.3]      
        ... 32 common frames omitted
Caused by: org.h2.jdbc.JdbcSQLIntegrityConstraintViolationException: NULL not allowed for column "REPORT_ID"; SQL statement:
insert into admin_query_list (access_level,db_driver_type,default_file_nm,email_event_id,email_from_address,file_ext,file_header_ind,file_transfer_type,input_data_fields,input_file
_key_length,input_file_key_start_pos,input_file_tx,is_active,is_visible,num_sheets,query,query_name,report_by_client_flag,report_db,report_db_ip_and_port,report_db_passwrd,report_d
b_server,report_db_userid,rerun_client_id,rerun_date_range_end,rerun_date_range_start,tab_delimited_flag,report_id) values (?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,default) [23502-232]
        at org.h2.message.DbException.getJdbcSQLException(DbException.java:520) ~[h2-2.3.232.jar:2.3.232]
        at org.h2.message.DbException.getJdbcSQLException(DbException.java:489) ~[h2-2.3.232.jar:2.3.232]
        at org.h2.message.DbException.get(DbException.java:223) ~[h2-2.3.232.jar:2.3.232]
        at org.h2.message.DbException.get(DbException.java:199) ~[h2-2.3.232.jar:2.3.232]
        at org.h2.table.Column.validateConvertUpdateSequence(Column.java:402) ~[h2-2.3.232.jar:2.3.232]
        at org.h2.table.Table.convertInsertRow(Table.java:963) ~[h2-2.3.232.jar:2.3.232]
        at org.h2.command.dml.Insert.insertRows(Insert.java:167) ~[h2-2.3.232.jar:2.3.232]
        at org.h2.command.dml.Insert.update(Insert.java:135) ~[h2-2.3.232.jar:2.3.232]
        at org.h2.command.CommandContainer.executeUpdateWithGeneratedKeys(CommandContainer.java:212) ~[h2-2.3.232.jar:2.3.232]
        at org.h2.command.CommandContainer.update(CommandContainer.java:133) ~[h2-2.3.232.jar:2.3.232]
        at org.h2.command.Command.executeUpdate(Command.java:304) ~[h2-2.3.232.jar:2.3.232]
        at org.h2.command.Command.executeUpdate(Command.java:248) ~[h2-2.3.232.jar:2.3.232]
        at org.h2.jdbc.JdbcPreparedStatement.executeUpdateInternal(JdbcPreparedStatement.java:213) ~[h2-2.3.232.jar:2.3.232]
        at org.h2.jdbc.JdbcPreparedStatement.executeUpdate(JdbcPreparedStatement.java:172) ~[h2-2.3.232.jar:2.3.232]
        at com.zaxxer.hikari.pool.ProxyPreparedStatement.executeUpdate(ProxyPreparedStatement.java:61) ~[HikariCP-5.1.0.jar:na]
        at com.zaxxer.hikari.pool.HikariProxyPreparedStatement.executeUpdate(HikariProxyPreparedStatement.java) ~[HikariCP-5.1.0.jar:na]
        at org.hibernate.engine.jdbc.internal.ResultSetReturnImpl.executeUpdate(ResultSetReturnImpl.java:194) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]
        ... 79 common frames omitted

[INFO] ------------------------------------------------------------------------
[INFO] BUILD FAILURE
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  14.182 s
[INFO] Finished at: 2025-05-10T11:31:19-05:00
[INFO] ------------------------------------------------------------------------
[ERROR] Failed to execute goal org.springframework.boot:spring-boot-maven-plugin:3.4.3:run (default-cli) on project admin: Process terminated with exit code: 1 -> [Help 1]
[ERROR]
[ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
[ERROR] Re-run Maven using the -X switch to enable full debug logging.
[ERROR]
[ERROR] For more information about the errors and possible solutions, please read the following articles:
[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/MojoExecutionException
PS C:\Users\F2LIPBX\spring_boot\2025-04-12\RAPIDadmin-microservices-java> 
