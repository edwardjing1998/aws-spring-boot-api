Deleted_Cases:

case_number	char	NO	12
pi_id	char	YES	16
customer_id	char	YES	24
primary_pi_id	char	YES	16
account	char	YES	16
last_name	char	YES	20
first_name	char	YES	16
hm_phone	char	YES	14
wk_phone	char	YES	14
entity_cd	char	YES	1
role_cd	char	YES	2
pi_status	char	YES	1
status	char	YES	1
active	bit	NO	NULL
reason	char	YES	1
subreason	int	YES	NULL
disposition	char	YES	1
in_hour	int	YES	NULL
in_date	datetime	YES	NULL
next_date	datetime	YES	NULL
out_date	datetime	YES	NULL
auto_date	datetime	YES	NULL
num_cards	int	YES	NULL
final_action_cards_nr	int	YES	NULL
delivery_id	int	YES	NULL
sys_prin	char	YES	8
cycle	char	YES	1
Frst_Updt_Vend_id	char	YES	3
Contact_cd	char	YES	1
Contact_Ph_nr	char	YES	18
Return_Reason_cd	char	YES	2
Issuance_cd	char	YES	1
issuance_dt	datetime	YES	NULL
WorkStation_name_tx	char	YES	18
Operator_CD	char	YES	2
Barcode_Type_CD	char	YES	1
rec_typ_tx	char	YES	2
srvc_typ_tx	char	YES	3
mailer_id	char	YES	9
AS400_client_id	char	YES	4
AS400_system_id	char	YES	8
bsc_spplmntl_id	char	YES	1
orig_ml_dt	datetime	YES	NULL
msg_id	char	YES	40
ml_mthd	char	YES	30
source_file	char	YES	16
cust_id	char	YES	9
ms_issue_date	char	YES	5
cust_id2	char	YES	9
mkt_cd	char	YES	2
account_tokenid	char	YES	16
pi_id_tokenid	char	YES	16
primary_pi_id_tokenid	char	YES	16

robot_labels

user_name	char	YES	25
reporting_date	smalldatetime	YES	NULL
bundles	int	YES	NULL
individual_labels	int	YES	NULL
robot_label_total	int	YES	NULL


labels

text1	varchar	YES	50
text2	varchar	YES	50
text3	varchar	YES	50
text4	varchar	YES	50
text5	varchar	YES	50
status	bit	NO	NULL
Type	varchar	YES	10
case_number	char	YES	16
card_type_tx	char	NO	7
Bar_Code_tx	varchar	YES	70
text3_Addr3	char	YES	50
text3_Addr4	char	YES	50






Hibernate: select dat1_0.case_number,dat1_0.date_time,dat1_0.trans_no,dat1_0.account,dat1_0.action_id,dat1_0.action_reason,dat1_0.document_no,dat1_0.no_cards,dat1_0.operator_time,d
at1_0.pi_id,dat1_0.postage_category_cd,dat1_0.sys_prin,dat1_0.uid,dat1_0.workstation_name_tx,t1_0.case_number,t1_0.date_time,t1_0.trans_no,t1_0.command_line,t1_0.cycle,t1_0.retry_c
ount,t1_0.system_type,t1_0.type from deleted_account_trans dat1_0 left join deleted_transactions t1_0 on dat1_0.case_number=t1_0.case_number and dat1_0.date_time=t1_0.date_time and dat1_0.trans_no=t1_0.trans_no where (dat1_0.case_number,dat1_0.date_time,dat1_0.trans_no) in ((?,?,?))
Hibernate: insert into deleted_account_trans (account,action_id,action_reason,document_no,no_cards,operator_time,pi_id,postage_category_cd,sys_prin,uid,workstation_name_tx,case_number,date_time,trans_no) values (?,?,?,?,?,?,?,?,?,?,?,?,?,?)
2025-05-27T12:30:20.866-05:00  WARN 19036 --- [           main] o.h.engine.jdbc.spi.SqlExceptionHelper   : SQL Error: 23505, SQLState: 23505
2025-05-27T12:30:20.867-05:00 ERROR 19036 --- [           main] o.h.engine.jdbc.spi.SqlExceptionHelper   : Unique index or primary key violation: "PUBLIC.PRIMARY_KEY_95 ON PUBLIC.D
ELETED_ACCOUNT_TRANS(CASE_NUMBER, TRANS_NO, DATE_TIME) VALUES ( /* key:54 */ CAST('CASE014     ' AS CHAR(12)), CAST(1014 AS NUMERIC(4)), TIMESTAMP '2025-05-27 12:30:20.863934')"; SQL statement:
insert into deleted_account_trans (account,action_id,action_reason,document_no,no_cards,operator_time,pi_id,postage_category_cd,sys_prin,uid,workstation_name_tx,case_number,date_time,trans_no) values (?,?,?,?,?,?,?,?,?,?,?,?,?,?) [23505-232]
2025-05-27T12:30:20.873-05:00  WARN 19036 --- [           main] ConfigServletWebServerApplicationContext : Exception encountered during context initialization - cancelling refresh attempt: org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'deletedCaseTransactionsDataGenerator': Invocation of init method failed
2025-05-27T12:30:20.875-05:00  INFO 19036 --- [           main] j.LocalContainerEntityManagerFactoryBean : Closing JPA EntityManagerFactory for persistence unit 'default'
2025-05-27T12:30:20.877-05:00  INFO 19036 --- [           main] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Shutdown initiated...
2025-05-27T12:30:20.880-05:00  INFO 19036 --- [           main] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Shutdown completed.
2025-05-27T12:30:20.882-05:00  INFO 19036 --- [           main] o.apache.catalina.core.StandardService   : Stopping service [Tomcat]
2025-05-27T12:30:20.894-05:00  INFO 19036 --- [           main] .s.b.a.l.ConditionEvaluationReportLogger :

Error starting ApplicationContext. To display the condition evaluation report re-run your application with 'debug' enabled.
2025-05-27T12:30:20.910-05:00 ERROR 19036 --- [           main] o.s.boot.SpringApplication               : Application run failed

org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'deletedCaseTransactionsDataGenerator': Invocation of init method failed
        at org.springframework.beans.factory.annotation.InitDestroyAnnotationBeanPostProcessor.postProcessBeforeInitialization(InitDestroyAnnotationBeanPostProcessor.java:222) ~[spring-beans-6.2.3.jar:6.2.3]
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.applyBeanPostProcessorsBeforeInitialization(AbstractAutowireCapableBeanFactory.java:423) ~[spring-beans-6.2.3.jar:6.2.3]
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.initializeBean(AbstractAutowireCapableBeanFactory.java:1804) ~[spring-beans-6.2.3.jar:6.2.3]
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.doCreateBean(AbstractAutowireCapableBeanFactory.java:601) ~[spring-beans-6.2.3.jar:6.2.3]   
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBean(AbstractAutowireCapableBeanFactory.java:523) ~[spring-beans-6.2.3.jar:6.2.3]     
        at org.springframework.beans.factory.support.AbstractBeanFactory.lambda$doGetBean$0(AbstractBeanFactory.java:339) ~[spring-beans-6.2.3.jar:6.2.3]
        at org.springframework.beans.factory.support.DefaultSingletonBeanRegistry.getSingleton(DefaultSingletonBeanRegistry.java:346) ~[spring-beans-6.2.3.jar:6.2.3]
        at org.springframework.beans.factory.support.AbstractBeanFactory.doGetBean(AbstractBeanFactory.java:337) ~[spring-beans-6.2.3.jar:6.2.3]
        at org.springframework.beans.factory.support.AbstractBeanFactory.getBean(AbstractBeanFactory.java:202) ~[spring-beans-6.2.3.jar:6.2.3]
        at org.springframework.beans.factory.support.DefaultListableBeanFactory.instantiateSingleton(DefaultListableBeanFactory.java:1155) ~[spring-beans-6.2.3.jar:6.2.3]
        at org.springframework.beans.factory.support.DefaultListableBeanFactory.preInstantiateSingleton(DefaultListableBeanFactory.java:1121) ~[spring-beans-6.2.3.jar:6.2.3]       
        at org.springframework.beans.factory.support.DefaultListableBeanFactory.preInstantiateSingletons(DefaultListableBeanFactory.java:1056) ~[spring-beans-6.2.3.jar:6.2.3]      
        at org.springframework.context.support.AbstractApplicationContext.finishBeanFactoryInitialization(AbstractApplicationContext.java:987) ~[spring-context-6.2.3.jar:6.2.3]    
        at org.springframework.context.support.AbstractApplicationContext.refresh(AbstractApplicationContext.java:627) ~[spring-context-6.2.3.jar:6.2.3]
        at org.springframework.boot.web.servlet.context.ServletWebServerApplicationContext.refresh(ServletWebServerApplicationContext.java:146) ~[spring-boot-3.4.3.jar:3.4.3]      
        at org.springframework.boot.SpringApplication.refresh(SpringApplication.java:752) ~[spring-boot-3.4.3.jar:3.4.3]
        at org.springframework.boot.SpringApplication.refreshContext(SpringApplication.java:439) ~[spring-boot-3.4.3.jar:3.4.3]
        at org.springframework.boot.SpringApplication.run(SpringApplication.java:318) ~[spring-boot-3.4.3.jar:3.4.3]
        at org.springframework.boot.SpringApplication.run(SpringApplication.java:1361) ~[spring-boot-3.4.3.jar:3.4.3]
        at org.springframework.boot.SpringApplication.run(SpringApplication.java:1350) ~[spring-boot-3.4.3.jar:3.4.3]
        at admin.RapidAdminApplication.main(RapidAdminApplication.java:12) ~[classes/:na]
Caused by: org.springframework.dao.DataIntegrityViolationException: could not execute statement [Unique index or primary key violation: "PUBLIC.PRIMARY_KEY_95 ON PUBLIC.DELETED_ACC
OUNT_TRANS(CASE_NUMBER, TRANS_NO, DATE_TIME) VALUES ( /* key:54 */ CAST('CASE014     ' AS CHAR(12)), CAST(1014 AS NUMERIC(4)), TIMESTAMP '2025-05-27 12:30:20.863934')"; SQL statement:
insert into deleted_account_trans (account,action_id,action_reason,document_no,no_cards,operator_time,pi_id,postage_category_cd,sys_prin,uid,workstation_name_tx,case_number,date_ti
me,trans_no) values (?,?,?,?,?,?,?,?,?,?,?,?,?,?) [23505-232]] [insert into deleted_account_trans (account,action_id,action_reason,document_no,no_cards,operator_time,pi_id,postage_
category_cd,sys_prin,uid,workstation_name_tx,case_number,date_time,trans_no) values (?,?,?,?,?,?,?,?,?,?,?,?,?,?)]; SQL [insert into deleted_account_trans (account,action_id,action
_reason,document_no,no_cards,operator_time,pi_id,postage_category_cd,sys_prin,uid,workstation_name_tx,case_number,date_time,trans_no) values (?,?,?,?,?,?,?,?,?,?,?,?,?,?)]; constraint [PUBLIC.PRIMARY_KEY_95]
        at org.springframework.orm.jpa.vendor.HibernateJpaDialect.convertHibernateAccessException(HibernateJpaDialect.java:290) ~[spring-orm-6.2.3.jar:6.2.3]
        at org.springframework.orm.jpa.vendor.HibernateJpaDialect.translateExceptionIfPossible(HibernateJpaDialect.java:241) ~[spring-orm-6.2.3.jar:6.2.3]
        at org.springframework.orm.jpa.JpaTransactionManager.doCommit(JpaTransactionManager.java:566) ~[spring-orm-6.2.3.jar:6.2.3]
        at org.springframework.transaction.support.AbstractPlatformTransactionManager.processCommit(AbstractPlatformTransactionManager.java:795) ~[spring-tx-6.2.3.jar:6.2.3]       
        at org.springframework.transaction.support.AbstractPlatformTransactionManager.commit(AbstractPlatformTransactionManager.java:758) ~[spring-tx-6.2.3.jar:6.2.3]
        at org.springframework.transaction.interceptor.TransactionAspectSupport.commitTransactionAfterReturning(TransactionAspectSupport.java:698) ~[spring-tx-6.2.3.jar:6.2.3]     
        at org.springframework.transaction.interceptor.TransactionAspectSupport.invokeWithinTransaction(TransactionAspectSupport.java:416) ~[spring-tx-6.2.3.jar:6.2.3]
        at org.springframework.transaction.interceptor.TransactionInterceptor.invoke(TransactionInterceptor.java:119) ~[spring-tx-6.2.3.jar:6.2.3]
        at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:184) ~[spring-aop-6.2.3.jar:6.2.3]
        at org.springframework.dao.support.PersistenceExceptionTranslationInterceptor.invoke(PersistenceExceptionTranslationInterceptor.java:138) ~[spring-tx-6.2.3.jar:6.2.3]      
        at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:184) ~[spring-aop-6.2.3.jar:6.2.3]
        at org.springframework.data.jpa.repository.support.CrudMethodMetadataPostProcessor$CrudMethodMetadataPopulatingMethodInterceptor.invoke(CrudMethodMetadataPostProcessor.java:165) ~[spring-data-jpa-3.4.3.jar:3.4.3]
        at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:184) ~[spring-aop-6.2.3.jar:6.2.3]
        at org.springframework.aop.framework.JdkDynamicAopProxy.invoke(JdkDynamicAopProxy.java:223) ~[spring-aop-6.2.3.jar:6.2.3]
        at jdk.proxy2/jdk.proxy2.$Proxy165.save(Unknown Source) ~[na:na]
        at admin.config.data.DeletedCaseTransactionsDataGenerator.generateData(DeletedCaseTransactionsDataGenerator.java:101) ~[classes/:na]
        at java.base/jdk.internal.reflect.DirectMethodHandleAccessor.invoke(DirectMethodHandleAccessor.java:104) ~[na:na]
        at java.base/java.lang.reflect.Method.invoke(Method.java:565) ~[na:na]
        at org.springframework.beans.factory.annotation.InitDestroyAnnotationBeanPostProcessor$LifecycleMethod.invoke(InitDestroyAnnotationBeanPostProcessor.java:457) ~[spring-beans-6.2.3.jar:6.2.3]
        at org.springframework.beans.factory.annotation.InitDestroyAnnotationBeanPostProcessor$LifecycleMetadata.invokeInitMethods(InitDestroyAnnotationBeanPostProcessor.java:401) ~[spring-beans-6.2.3.jar:6.2.3]
        at org.springframework.beans.factory.annotation.InitDestroyAnnotationBeanPostProcessor.postProcessBeforeInitialization(InitDestroyAnnotationBeanPostProcessor.java:219) ~[spring-beans-6.2.3.jar:6.2.3]
        ... 20 common frames omitted
Caused by: org.hibernate.exception.ConstraintViolationException: could not execute statement [Unique index or primary key violation: "PUBLIC.PRIMARY_KEY_95 ON PUBLIC.DELETED_ACCOUNT_TRANS(CASE_NUMBER, TRANS_NO, DATE_TIME) VALUES ( /* key:54 */ CAST('CASE014     ' AS CHAR(12)), CAST(1014 AS NUMERIC(4)), TIMESTAMP '2025-05-27 12:30:20.863934')"; SQL statement:
insert into deleted_account_trans (account,action_id,action_reason,document_no,no_cards,operator_time,pi_id,postage_category_cd,sys_prin,uid,workstation_name_tx,case_number,date_ti
me,trans_no) values (?,?,?,?,?,?,?,?,?,?,?,?,?,?) [23505-232]] [insert into deleted_account_trans (account,action_id,action_reason,document_no,no_cards,operator_time,pi_id,postage_category_cd,sys_prin,uid,workstation_name_tx,case_number,date_time,trans_no) values (?,?,?,?,?,?,?,?,?,?,?,?,?,?)]
        at org.hibernate.dialect.H2Dialect.lambda$buildSQLExceptionConversionDelegate$3(H2Dialect.java:759) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]
        at org.hibernate.exception.internal.StandardSQLExceptionConverter.convert(StandardSQLExceptionConverter.java:58) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]
        at org.hibernate.engine.jdbc.spi.SqlExceptionHelper.convert(SqlExceptionHelper.java:108) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]
        at org.hibernate.engine.jdbc.internal.ResultSetReturnImpl.executeUpdate(ResultSetReturnImpl.java:197) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]
        at org.hibernate.engine.jdbc.mutation.internal.AbstractMutationExecutor.performNonBatchedMutation(AbstractMutationExecutor.java:134) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]
        at org.hibernate.engine.jdbc.mutation.internal.MutationExecutorSingleNonBatched.performNonBatchedOperations(MutationExecutorSingleNonBatched.java:55) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]
        at org.hibernate.engine.jdbc.mutation.internal.AbstractMutationExecutor.execute(AbstractMutationExecutor.java:55) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]
        at org.hibernate.persister.entity.mutation.InsertCoordinatorStandard.doStaticInserts(InsertCoordinatorStandard.java:194) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]      
        at org.hibernate.persister.entity.mutation.InsertCoordinatorStandard.coordinateInsert(InsertCoordinatorStandard.java:132) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]     
        at org.hibernate.persister.entity.mutation.InsertCoordinatorStandard.insert(InsertCoordinatorStandard.java:104) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]
        at org.hibernate.action.internal.EntityInsertAction.execute(EntityInsertAction.java:110) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]
        at org.hibernate.engine.spi.ActionQueue.executeActions(ActionQueue.java:644) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]
        at org.hibernate.engine.spi.ActionQueue.executeActions(ActionQueue.java:511) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]
        at org.hibernate.event.internal.AbstractFlushingEventListener.performExecutions(AbstractFlushingEventListener.java:414) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]       
        at org.hibernate.event.internal.DefaultFlushEventListener.onFlush(DefaultFlushEventListener.java:41) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]
        at org.hibernate.event.service.internal.EventListenerGroupImpl.fireEventOnEachListener(EventListenerGroupImpl.java:127) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]       
        at org.hibernate.internal.SessionImpl.doFlush(SessionImpl.java:1429) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]
        at org.hibernate.internal.SessionImpl.managedFlush(SessionImpl.java:491) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]
        at org.hibernate.internal.SessionImpl.flushBeforeTransactionCompletion(SessionImpl.java:2354) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]
        at org.hibernate.internal.SessionImpl.beforeTransactionCompletion(SessionImpl.java:1978) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]
        at org.hibernate.engine.jdbc.internal.JdbcCoordinatorImpl.beforeTransactionCompletion(JdbcCoordinatorImpl.java:439) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]
        at org.hibernate.resource.transaction.backend.jdbc.internal.JdbcResourceLocalTransactionCoordinatorImpl.beforeCompletionCallback(JdbcResourceLocalTransactionCoordinatorImpl.java:169) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]
        at org.hibernate.resource.transaction.backend.jdbc.internal.JdbcResourceLocalTransactionCoordinatorImpl$TransactionDriverControlImpl.commit(JdbcResourceLocalTransactionCoordinatorImpl.java:267) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]
        at org.hibernate.engine.transaction.internal.TransactionImpl.commit(TransactionImpl.java:101) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]
        at org.springframework.orm.jpa.JpaTransactionManager.doCommit(JpaTransactionManager.java:562) ~[spring-orm-6.2.3.jar:6.2.3]
        ... 38 common frames omitted
Caused by: org.h2.jdbc.JdbcSQLIntegrityConstraintViolationException: Unique index or primary key violation: "PUBLIC.PRIMARY_KEY_95 ON PUBLIC.DELETED_ACCOUNT_TRANS(CASE_NUMBER, TRANS_NO, DATE_TIME) VALUES ( /* key:54 */ CAST('CASE014     ' AS CHAR(12)), CAST(1014 AS NUMERIC(4)), TIMESTAMP '2025-05-27 12:30:20.863934')"; SQL statement:
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
        at com.zaxxer.hikari.pool.ProxyPreparedStatement.executeUpdate(ProxyPreparedStatement.java:61) ~[HikariCP-5.1.0.jar:na]
        at com.zaxxer.hikari.pool.HikariProxyPreparedStatement.executeUpdate(HikariProxyPreparedStatement.java) ~[HikariCP-5.1.0.jar:na]
        at org.hibernate.engine.jdbc.internal.ResultSetReturnImpl.executeUpdate(ResultSetReturnImpl.java:194) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]
        ... 59 common frames omitted

[INFO] ------------------------------------------------------------------------
[INFO] BUILD FAILURE
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  21.407 s
[INFO] Finished at: 2025-05-27T12:30:21-05:00
[INFO] ------------------------------------------------------------------------
[ERROR] Failed to execute goal org.springframework.boot:spring-boot-maven-plugin:3.4.3:run (default-cli) on project admin: Process terminated with exit code: 1 -> [Help 1]
[ERROR]
[ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
[ERROR] Re-run Maven using the -X switch to enable full debug logging.
[ERROR]
[ERROR] For more information about the errors and possible solutions, please read the following articles:
[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/MojoExecutionException
PS C:\Users\F2LIPBX\RAPIDadmin-microservices-java> 


















