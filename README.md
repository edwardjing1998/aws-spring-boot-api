2026-01-16T20:15:02.101Z  INFO 1 --- [.0-10443-exec-6] c.w.client.web.ClientWriterController    : Saving new client with ID: 0016
Hibernate: select c1_0.client,c1_0.active,c1_0.addr,c1_0.amex_issued,c1_0.billing_sp,c1_0.chlookup_type,c1_0.city,c1_0.contact,c1_0.exclude_from_report,c1_0.fax_number,c1_0.name,c1_0.phone,c1_0.positive_reports,c1_0.report_break_flag,c1_0.state,c1_0.sub_client_ind,c1_0.sub_client_xref,c1_0.zip,ce1_0.client_id,ce1_0.email_address_tx,ce1_0.active_flag,ce1_0.carbon_copy_flag,ce1_0.email_name_tx,ce1_0.mail_server_id,ce1_0.report_id from clients c1_0 left join client_email ce1_0 on c1_0.client=ce1_0.client_id where c1_0.client=?
Hibernate: select spp1_0.billing_sp,spp1_0.atm_cash_rule,spp1_0.prefix from sys_prins_prefix spp1_0 where spp1_0.billing_sp=?
Hibernate: select sp1_0.client,sp1_0.sys_prin,sp1_0.addr_flag,sp1_0.a_stat_rch,sp1_0.bad_state,sp1_0.cust_type,sp1_0.des_stat,sp1_0.entity_cd,sp1_0.forwarding_addr,sp1_0.hold_days,sp1_0.nm_13,sp1_0.non_us,sp1_0.notes,sp1_0.pin,sp1_0.po_box,sp1_0.report_method,sp1_0.ret_stat,sp1_0.rps,sp1_0.session,sp1_0.special,sp1_0.stat_a,sp1_0.stat_b,sp1_0.stat_c,sp1_0.stat_d,sp1_0.stat_e,sp1_0.stat_f,sp1_0.stat_i,sp1_0.stat_l,sp1_0.stat_o,sp1_0.stat_u,sp1_0.stat_x,sp1_0.stat_z,sp1_0.active,sp1_0.contact,sp1_0.phone,sp1_0.temp_away,sp1_0.temp_away_atts,sp1_0.undeliverable from sys_prins sp1_0 where sp1_0.client=?
Hibernate: select ro1_0.client_id,ro1_0.report_id,ro1_0.email_body_tx,ro1_0.email_flag,ro1_0.file_type_cd,ro1_0.output_type_cd,ro1_0.receive_flag,rd1_0.report_id,rd1_0.access_level,cft1_0.file_trns_id,cft1_0.org_type_cd,cft1_0.bin_file_crlf_ind,cft1_0.block_size_nr,cft1_0.control_file_nm,cft1_0.convert_file_cd,cft1_0.dd_nm,cft1_0.gateway_access_cd,cft1_0.ip_port_cd,cft1_0.job_nm,cft1_0.listener_srv_nm,cft1_0.local_file_nm,cft1_0.member_cd,cft1_0.mode_nm,cft1_0.program_nm,cft1_0.protocol_nm,cft1_0.record_lgth_nr,cft1_0.remote_file_nm,cft1_0.security_nm,cft1_0.sequence_nr,cft1_0.trans_prg_nm,cft1_0.transfer_cd,cft1_0.xfer_file_nm,rd1_0.db_driver_type,rd1_0.default_file_nm,rd1_0.email_event_id,rd1_0.email_from_address,rd1_0.file_ext,rd1_0.file_header_ind,rd1_0.file_transfer_type,rd1_0.input_data_fields,rd1_0.input_file_key_length,rd1_0.input_file_key_start_pos,rd1_0.input_file_tx,rd1_0.is_active,rd1_0.is_visible,rd1_0.num_sheets,rd1_0.query,rd1_0.query_name,rd1_0.report_by_client_flag,rd1_0.report_db,rd1_0.report_db_ip_and_port,rd1_0.report_db_passwrd,rd1_0.report_db_server,rd1_0.report_db_userid,rd1_0.rerun_client_id,rd1_0.rerun_date_range_end,rd1_0.rerun_date_range_start,rd1_0.tab_delimited_flag,ro1_0.report_password_tx from client_report_options ro1_0 left join admin_query_list rd1_0 on rd1_0.report_id=ro1_0.report_id left join c3_transfer_parameters cft1_0 on cft1_0.file_trns_id=rd1_0.report_id where ro1_0.client_id=?
Hibernate: update clients set active=?,addr=?,amex_issued=?,billing_sp=?,chlookup_type=?,city=?,contact=?,exclude_from_report=?,fax_number=?,name=?,phone=?,positive_reports=?,report_break_flag=?,state=?,sub_client_ind=?,sub_client_xref=?,zip=? where client=?
2026-01-16T20:15:07.495Z  WARN 1 --- [.0-10443-exec-6] o.h.engine.jdbc.spi.SqlExceptionHelper   : SQL Error: 8152, SQLState: 22001
2026-01-16T20:15:07.496Z ERROR 1 --- [.0-10443-exec-6] o.h.engine.jdbc.spi.SqlExceptionHelper   : String or binary data would be truncated.
2026-01-16T20:15:07.997Z ERROR 1 --- [.0-10443-exec-6] o.a.c.c.C.[.[.[/].[dispatcherServlet]    : Servlet.service() for servlet [dispatcherServlet] in context with path [] threw exception [Request processing failed: org.springframework.dao.DataIntegrityViolationException: could not execute statement [String or binary data would be truncated.] [update clients set active=?,addr=?,amex_issued=?,billing_sp=?,chlookup_type=?,city=?,contact=?,exclude_from_report=?,fax_number=?,name=?,phone=?,positive_reports=?,report_break_flag=?,state=?,sub_client_ind=?,sub_client_xref=?,zip=? where client=?]; SQL [update clients set active=?,addr=?,amex_issued=?,billing_sp=?,chlookup_type=?,city=?,contact=?,exclude_from_report=?,fax_number=?,name=?,phone=?,positive_reports=?,report_break_flag=?,state=?,sub_client_ind=?,sub_client_xref=?,zip=? where client=?]] with root cause

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
	at org.hibernate.engine.jdbc.internal.ResultSetReturnImpl.executeUpdate(ResultSetReturnImpl.java:194) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
	at org.hibernate.engine.jdbc.mutation.internal.AbstractMutationExecutor.performNonBatchedMutation(AbstractMutationExecutor.java:134) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
	at org.hibernate.engine.jdbc.mutation.internal.MutationExecutorSingleNonBatched.performNonBatchedOperations(MutationExecutorSingleNonBatched.java:55) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
	at org.hibernate.engine.jdbc.mutation.internal.AbstractMutationExecutor.execute(AbstractMutationExecutor.java:55) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
	at org.hibernate.persister.entity.mutation.UpdateCoordinatorStandard.doStaticUpdate(UpdateCoordinatorStandard.java:781) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
	at org.hibernate.persister.entity.mutation.UpdateCoordinatorStandard.performUpdate(UpdateCoordinatorStandard.java:328) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
	at org.hibernate.persister.entity.mutation.UpdateCoordinatorStandard.update(UpdateCoordinatorStandard.java:245) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
	at org.hibernate.action.internal.EntityUpdateAction.execute(EntityUpdateAction.java:169) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
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
	at org.springframework.transaction.support.AbstractPlatformTransactionManager.processCommit(AbstractPlatformTransactionManager.java:795) ~[spring-tx-6.2.7.jar:6.2.7]
	at org.springframework.transaction.support.AbstractPlatformTransactionManager.commit(AbstractPlatformTransactionManager.java:758) ~[spring-tx-6.2.7.jar:6.2.7]
	at org.springframework.transaction.interceptor.TransactionAspectSupport.commitTransactionAfterReturning(TransactionAspectSupport.java:698) ~[spring-tx-6.2.7.jar:6.2.7]
	at org.springframework.transaction.interceptor.TransactionAspectSupport.invokeWithinTransaction(TransactionAspectSupport.java:416) ~[spring-tx-6.2.7.jar:6.2.7]
	at org.springframework.transaction.interceptor.TransactionInterceptor.invoke(TransactionInterceptor.java:119) ~[spring-tx-6.2.7.jar:6.2.7]
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:184) ~[spring-aop-6.2.7.jar:6.2.7]
	at org.springframework.dao.support.PersistenceExceptionTranslationInterceptor.invoke(PersistenceExceptionTranslationInterceptor.java:138) ~[spring-tx-6.2.7.jar:6.2.7]
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:184) ~[spring-aop-6.2.7.jar:6.2.7]
	at org.springframework.data.jpa.repository.support.CrudMethodMetadataPostProcessor$CrudMethodMetadataPopulatingMethodInterceptor.invoke(CrudMethodMetadataPostProcessor.java:165) ~[spring-data-jpa-3.5.0.jar:3.5.0]
	at org.springfra
