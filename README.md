2025-12-07T16:59:31.190-06:00 ERROR 25156 --- [client-sysprin-reader] [0.0-8083-exec-1] o.a.c.c.C.[.[.[/].[dispatcherServlet]    : Servlet.service() for servlet [dispatcherServlet] in context with path [] threw exception [Request processing failed: org.springframework.jdbc.UncategorizedSQLException: PreparedStatementCallback; uncategorized SQLException for SQL [SELECT
            (
              SELECT
                  distinct(c.client)  AS [client],
                  c.name              AS [name],
                  c.addr              AS [addr],
                  c.city              AS [city],
                  c.state             AS [state],
                  c.zip               AS [zip],
                  c.contact           AS [contact],
                  c.phone             AS [phone],
                  c.active            AS [active],
                  c.fax_number        AS [faxNumber],
                  c.billing_sp        AS [billingSp],
                  c.report_break_flag AS [reportBreakFlag],
                  c.chlookup_type     AS [chLookUpType],
                  c.exclude_from_report AS [excludeFromReport],
                  c.positive_reports  AS [positiveReports],
                  c.sub_client_ind    AS [subClientInd],
                  c.sub_client_xref   AS [subClientXref],
                  c.amex_issued       AS [amexIssued],

                /* ? BEST PRACTICE: Add this subquery here.
                     It calculates the total number of sys_prins for the client
                     efficiently without fetching all rows. */
                  (SELECT COUNT(*)
                   FROM sys_prins sp_cnt
                   WHERE sp_cnt.client = c.client
                  ) AS [sysPrinTotal],

                  /* ---------- reportOptions (array) ---------- */
                  JSON_QUERY((
                    SELECT
                      ro.client_id          AS [clientId],
                      ro.report_id          AS [reportId],
                      ro.receive_flag       AS [receiveFlag],
                      ro.output_type_cd     AS [outputTypeCd],
                      ro.file_type_cd       AS [fileTypeCd],
                      ro.email_flag         AS [emailFlag],
                      ro.email_body_tx      AS [emailBodyTx],
                      ro.report_password_tx AS [reportPasswordTx],

                      /* reportDetails (single object) */
                      JSON_QUERY((
                        SELECT
                          rd.report_id              AS [reportId],
                          rd.query_name             AS [queryName],
                          rd.query                  AS [query],
                          rd.input_data_fields      AS [inputDataFields],
                          rd.file_ext               AS [fileExt],
                          rd.db_driver_type         AS [dbDriverType],
                          rd.file_header_ind        AS [fileHeaderInd],
                          rd.default_file_nm        AS [defaultFileNm],
                          rd.report_db_server       AS [reportDbServer],
                          rd.report_db              AS [reportDb],
                          rd.report_db_userid       AS [reportDbUserid],
                          rd.report_db_passwrd      AS [reportDbPasswrd],
                          rd.file_transfer_type     AS [fileTransferType],
                          rd.report_db_ip_and_port  AS [reportDbIpAndPort],
                          rd.report_by_client_flag  AS [reportByClientFlag],
                          rd.rerun_date_range_start AS [rerunDateRangeStart],
                          rd.rerun_date_range_end   AS [rerunDateRangeEnd],
                          rd.rerun_client_id        AS [rerunClientId],
                          rd.email_from_address     AS [emailFromAddress],
                          rd.email_event_id         AS [emailEventId],
                          rd.tab_delimited_flag     AS [tabDelimitedFlag],
                          rd.input_file_tx          AS [inputFileTx],
                          rd.input_file_key_start_pos AS [inputFileKeyStartPos],
                          rd.input_file_key_length  AS [inputFileKeyLength],
                          rd.access_level           AS [accessLevel],
                          rd.is_active              AS [isActive],
                          rd.is_visible             AS [isVisible],
                          rd.num_sheets             AS [numSheets],

                          /* c3FileTransfer (single object) */
                          JSON_QUERY((
                            SELECT
                              ft.file_trns_id      AS [fileTransId],
                              ft.sequence_nr       AS [sequenceNr],
                              ft.transfer_cd       AS [transferCd],
                              ft.protocol_nm       AS [protocolNm],
                              ft.trans_prg_nm      AS [transPrgNm],
                              ft.ip_port_cd        AS [ipPortCd],
                              ft.block_size_nr     AS [blockSizeNr],
                              ft.convert_file_cd   AS [convertFileCd],
                              ft.mode_nm           AS [modeNm],
                              ft.security_nm       AS [securityNm],
                              ft.xfer_file_nm      AS [xferFileNm],
                              ft.dd_nm             AS [ddNm],
                              ft.member_cd         AS [memberCd],
                              ft.job_nm            AS [jobNm],
                              ft.remote_file_nm    AS [remoteFileNm],
                              ft.gateway_access_cd AS [gatewayAccessCd],
                              ft.listener_srv_nm   AS [listenerSrvNm],
                              ft.org_type_cd       AS [orgTypeCd],
                              ft.program_nm        AS [programNm],
                              ft.bin_file_CRLF_ind AS [binFileCRLFInf],
                              ft.control_file_nm   AS [controlFileNm],
                              ft.record_lgth_nr    AS [recordLengthNr],
                              ft.local_file_nm     AS [localFileNm]
                            FROM c3_transfer_parameters ft
                            WHERE ft.file_trns_id = rd.report_id
                            FOR JSON PATH, WITHOUT_ARRAY_WRAPPER
                          )) AS [c3FileTransfer]
                        FROM ADMIN_QUERY_LIST rd
                        WHERE rd.report_id = ro.report_id
                        FOR JSON PATH, WITHOUT_ARRAY_WRAPPER
                      )) AS [reportDetails]

                    FROM CLIENT_REPORT_OPTIONS ro
                    WHERE ro.client_id = c.client
                    ORDER BY ro.report_id
                    FOR JSON PATH
                  )) AS [reportOptions],

                  /* ---------- sysPrinsPrefixes (array) ---------- */
                  JSON_QUERY((
                    SELECT
                      spp.billing_sp    AS [billingSp],
                      spp.prefix        AS [prefix],
                      spp.atm_cash_rule AS [atmCashRule]
                    FROM sys_prins_prefix spp
                    WHERE spp.BILLING_SP = c.billing_sp
                    ORDER BY spp.prefix
                    FOR JSON PATH
                  )) AS [sysPrinsPrefixes],

                  /* ---------- clientEmail (array) ---------- */
                  JSON_QUERY((
                    SELECT
                      ce.client_id        AS [clientId],
                      ce.email_address_tx AS [emailAddressTx],
                      ce.report_id        AS [reportId],
                      ce.email_name_tx    AS [emailNameTx],
                      ce.carbon_copy_flag AS [carbonCopyFlag],
                      ce.active_flag      AS [activeFlag],
                      ce.mail_server_id   AS [mailServerId]
                    FROM CLIENT_EMAIL ce
                    WHERE ce.client_id = c.client
                    ORDER BY ce.report_id, ce.email_address_tx
                    FOR JSON PATH
                  )) AS [clientEmail],

                  /* ---------- sysPrins (array) ---------- */
                  JSON_QUERY((
                    SELECT
                      sp.client          AS [client],
                      sp.sys_prin        AS [sysPrin],
                      sp.cust_type       AS [custType],
                      sp.undeliverable   AS [undeliverable],
                      sp.stat_a          AS [statA],
                      sp.stat_b          AS [statB],
                      sp.stat_c          AS [statC],
                      sp.stat_d          AS [statD],
                      sp.stat_e          AS [statE],
                      sp.stat_f          AS [statF],
                      sp.stat_i          AS [statI],
                      sp.stat_l          AS [statL],
                      sp.stat_o          AS [statO],
                      sp.stat_u          AS [statU],
                      sp.stat_x          AS [statX],
                      sp.stat_z          AS [statZ],
                      sp.po_box          AS [poBox],
                      sp.addr_flag       AS [addrFlag],
                      sp.temp_away       AS [tempAway],
                      sp.rps             AS [rps],
                      sp.session         AS [session],
                      sp.bad_state       AS [badState],
                      sp.A_STAT_RCH      AS [astatRch],
                      sp.NM_13           AS [nm13],
                      sp.temp_away_atts  AS [tempAwayAtts],
                      sp.report_method   AS [reportMethod],
                      sp.active          AS [sysPrinActive],
                      sp.notes           AS [notes],
                      sp.RET_STAT        AS [returnStatus],
                      sp.DES_STAT        AS [destroyStatus],
                      sp.non_us          AS [nonUS],
                      sp.special         AS [special],
                      sp.pin             AS [pinMailer],
                      sp.hold_days       AS [holdDays],
                      sp.FORWARDING_ADDR AS [forwardingAddress],
                      sp.contact         AS [sysPrinContact],
                      sp.phone           AS [sysPrinPhone],
                      sp.ENTITY_CD       AS [entityCode],

                      /* invalidDelivAreas (array) */
                      JSON_QUERY((
                        SELECT
                          ida.area     AS [area],
                          ida.sys_prin AS [sysPrin]
                        FROM invalid_deliv_areas ida
                        WHERE ida.sys_prin = sp.sys_prin
                        FOR JSON PATH
                      )) AS [invalidDelivAreas],

                      /* vendorSentTo (array; IO='I') */
                      JSON_QUERY((
                        SELECT
                          vst.sys_prin      AS [sysPrin],
                          vst.vend_id       AS [vendorId],
                          vst.queformail_cd AS [queForMail],
                          JSON_QUERY((
                            SELECT
                              v.vend_id                     AS [id],
                              v.vend_nm                     AS [name],
                              v.vend_actv_cd                AS [active],
                              v.vend_rcvr_cd                AS [receiver],
                              v.vend_fsrv_nm                AS [fileServerName],
                              v.vend_fsrv_ip                AS [fileServerIp],
                              v.fsrvr_user_id               AS [fileServerUserId],
                              v.fsrvr_usr_pwd_tx            AS [fileServerPassword],
                              v.fsrvr_file_name_tx          AS [fileName],
                              v.fsrvr_unc_share_tx          AS [uncShare],
                              v.fsrvr_path_tx               AS [path],
                              v.fsrvr_file_archive_path_tx  AS [archivePath],
                              v.vendor_type_cd              AS [vendorTypeCode],
                              v.vend_file_io                AS [fileIo],
                              v.vend_client_specific        AS [clientSpecific],
                              v.vend_schedule               AS [schedule],
                              v.vend_date_last_worked       AS [dateLastWorked],
                              v.vend_filesize               AS [fileSize],
                              v.vend_filetrans_specs        AS [fileTransferSpecs],
                              v.vend_file_type              AS [fileType],
                              v.ftp_passive                 AS [ftpPassive],
                              v.ftp_filetype                AS [ftpFileType]
                            FROM VENDOR v
                            WHERE v.vend_id = vst.vend_id
                              AND v.vend_file_io = 'I'
                            FOR JSON PATH, WITHOUT_ARRAY_WRAPPER
                          )) AS [vendor]
                        FROM vendor_sent_to vst
                        WHERE vst.sys_prin = sp.sys_prin
                          AND EXISTS (
                            SELECT 1
                            FROM VENDOR vv
                            WHERE vv.vend_id = vst.vend_id
                              AND vv.vend_file_io = 'I'
                          )
                        FOR JSON PATH
                      )) AS [vendorSentTo],

                      /* vendorReceivedFrom (array; IO='O') */
                      JSON_QUERY((
                        SELECT
                          vrf.sys_prin      AS [sysPrin],
                          vrf.vend_id       AS [vendorId],
                          vrf.queformail_cd AS [queForMail],
                          JSON_QUERY((
                            SELECT
                              v.vend_id                     AS [id],
                              v.vend_nm                     AS [name],
                              v.vend_actv_cd                AS [active],
                              v.vend_rcvr_cd                AS [receiver],
                              v.vend_fsrv_nm                AS [fileServerName],
                              v.vend_fsrv_ip                AS [fileServerIp],
                              v.fsrvr_user_id               AS [fileServerUserId],
                              v.fsrvr_usr_pwd_tx            AS [fileServerPassword],
                              v.fsrvr_file_name_tx          AS [fileName],
                              v.fsrvr_unc_share_tx          AS [uncShare],
                              v.fsrvr_path_tx               AS [path],
                              v.fsrvr_file_archive_path_tx  AS [archivePath],
                              v.vendor_type_cd              AS [vendorTypeCode],
                              v.vend_file_io                AS [fileIo],
                              v.vend_client_specific        AS [clientSpecific],
                              v.vend_schedule               AS [schedule],
                              v.vend_date_last_worked       AS [dateLastWorked],
                              v.vend_filesize               AS [fileSize],
                              v.vend_filetrans_specs        AS [fileTransferSpecs],
                              v.vend_file_type              AS [fileType],
                              v.ftp_passive                 AS [ftpPassive],
                              v.ftp_filetype                AS [ftpFileType]
                            FROM VENDOR v
                            WHERE v.vend_id = vrf.vend_id
                              AND v.vend_file_io = 'O'
                            FOR JSON PATH, WITHOUT_ARRAY_WRAPPER
                          )) AS [vendor]
                        FROM vendor_sent_to vrf
                        WHERE vrf.sys_prin = sp.sys_prin
                          AND EXISTS (
                            SELECT 1
                            FROM VENDOR vv
                            WHERE vv.vend_id = vrf.vend_id
                              AND vv.vend_file_io = 'O'
                          )
                        FOR JSON PATH
                      )) AS [vendorReceivedFrom]

                    FROM sys_prins sp
                    WHERE sp.client = c.client

                    /* ? YOUR CHANGE STARTS HERE */
                      FROM (
                          SELECT
                              *,
                              /* 1. Calculate Row Number partitioned by the ID you want unique */
                              ROW_NUMBER() OVER (
                                  PARTITION BY sys_prin
                                  ORDER BY active DESC, sys_prin
                              ) as rn
                          FROM sys_prins
                          /* 2. Correlate with the outer 'clients c' table here for performance */
                          WHERE client = c.client
                      ) sp
                      /* 3. Filter to keep only the 1st instance */
                      WHERE sp.rn = 1
                      /* ? YOUR CHANGE ENDS HERE */

                    ORDER BY sp.sys_prin
                    OFFSET (? * ?) ROWS FETCH NEXT ? ROWS ONLY
                    FOR JSON PATH
                  )) AS [sysPrins]

              FROM clients c
              WHERE c.client  = ?   -- ? filter by single client
              FOR JSON PATH
            ) AS full_json;]; SQL state [S0001]; error code [156]; Incorrect syntax near the keyword 'FROM'.] with root cause

com.microsoft.sqlserver.jdbc.SQLServerException: Incorrect syntax near the keyword 'FROM'.
        at com.microsoft.sqlserver.jdbc.SQLServerException.makeFromDatabaseError(SQLServerException.java:276) ~[mssql-jdbc-12.10.0.jre11.jar:na]
        at com.microsoft.sqlserver.jdbc.SQLServerStatement.getNextResult(SQLServerStatement.java:1787) ~[mssql-jdbc-12.10.0.jre11.jar:na]
        at com.microsoft.sqlserver.jdbc.SQLServerPreparedStatement.doExecutePreparedStatement(SQLServerPreparedStatement.java:688) ~[mssql-jdbc-12.10.0.jre11.jar:na]
        at com.microsoft.sqlserver.jdbc.SQLServerPreparedStatement$PrepStmtExecCmd.doExecute(SQLServerPreparedStatement.java:607) ~[mssql-jdbc-12.10.0.jre11.jar:na]
        at com.microsoft.sqlserver.jdbc.TDSCommand.execute(IOBuffer.java:7745) ~[mssql-jdbc-12.10.0.jre11.jar:na]
        at com.microsoft.sqlserver.jdbc.SQLServerConnection.executeCommand(SQLServerConnection.java:4700) ~[mssql-jdbc-12.10.0.jre11.jar:na]
        at com.microsoft.sqlserver.jdbc.SQLServerStatement.executeCommand(SQLServerStatement.java:321) ~[mssql-jdbc-12.10.0.jre11.jar:na]
        at com.microsoft.sqlserver.jdbc.SQLServerStatement.executeStatement(SQLServerStatement.java:253) ~[mssql-jdbc-12.10.0.jre11.jar:na]
        at com.microsoft.sqlserver.jdbc.SQLServerPreparedStatement.executeQuery(SQLServerPreparedStatement.java:521) ~[mssql-jdbc-12.10.0.jre11.jar:na]
        at com.zaxxer.hikari.pool.ProxyPreparedStatement.executeQuery(ProxyPreparedStatement.java:52) ~[HikariCP-6.3.0.jar:na]
        at com.zaxxer.hikari.pool.HikariProxyPreparedStatement.executeQuery(HikariProxyPreparedStatement.java) ~[HikariCP-6.3.0.jar:na]
        at org.springframework.jdbc.core.JdbcTemplate$1.doInPreparedStatement(JdbcTemplate.java:732) ~[spring-jdbc-6.2.7.jar:6.2.7]
        at org.springframework.jdbc.core.JdbcTemplate.execute(JdbcTemplate.java:658) ~[spring-jdbc-6.2.7.jar:6.2.7]
        at org.springframework.jdbc.core.JdbcTemplate.query(JdbcTemplate.java:723) ~[spring-jdbc-6.2.7.jar:6.2.7]
        at org.springframework.jdbc.core.JdbcTemplate.query(JdbcTemplate.java:748) ~[spring-jdbc-6.2.7.jar:6.2.7]
        at org.springframework.jdbc.core.JdbcTemplate.query(JdbcTemplate.java:804) ~[spring-jdbc-6.2.7.jar:6.2.7]
        at org.springframework.jdbc.core.namedparam.NamedParameterJdbcTemplate.queryForObject(NamedParameterJdbcTemplate.java:252) ~[spring-jdbc-6.2.7.jar:6.2.7]
        at org.springframework.jdbc.core.namedparam.NamedParameterJdbcTemplate.queryForObject(NamedParameterJdbcTemplate.java:269) ~[spring-jdbc-6.2.7.jar:6.2.7]
        at rapid.repository.client.ClientDetailDaoWithNativeSql.fetchClientDetailByClientId(ClientDetailDaoWithNativeSql.java:35) ~[common-model-0.0.1-SNAPSHOT.jar:na]
        at java.base/jdk.internal.reflect.DirectMethodHandleAccessor.invoke(DirectMethodHandleAccessor.java:104) ~[na:na]
        at java.base/java.lang.reflect.Method.invoke(Method.java:565) ~[na:na]
        at org.springframework.aop.support.AopUtils.invokeJoinpointUsingReflection(AopUtils.java:359) ~[spring-aop-6.2.7.jar:6.2.7]
        at org.springframework.aop.framework.ReflectiveMethodInvocation.invokeJoinpoint(ReflectiveMethodInvocation.java:196) ~[spring-aop-6.2.7.jar:6.2.7]
        at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:163) ~[spring-aop-6.2.7.jar:6.2.7]
        at org.springframework.dao.support.PersistenceExceptionTranslationInterceptor.invoke(PersistenceExceptionTranslationInterceptor.java:138) ~[spring-tx-6.2.7.jar:6.2.7]
        at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:184) ~[spring-aop-6.2.7.jar:6.2.7]
        at org.springframework.aop.framework.CglibAopProxy$DynamicAdvisedInterceptor.intercept(CglibAopProxy.java:728) ~[spring-aop-6.2.7.jar:6.2.7]
        at rapid.repository.client.ClientDetailDaoWithNativeSql$$SpringCGLIB$$0.fetchClientDetailByClientId(<generated>) ~[common-model-0.0.1-SNAPSHOT.jar:na]
        at rapid.service.client.ClientNativeSqlService.fetchClientDetailJsonByClientIdPage(ClientNativeSqlService.java:26) ~[common-services-0.0.1-SNAPSHOT.jar:na]
        at rapid.client.web.ClientController.getClientJsonByClientId(ClientController.java:80) ~[classes/:na]
        at java.base/jdk.internal.reflect.DirectMethodHandleAccessor.invoke(DirectMethodHandleAccessor.java:104) ~[na:na]
        at java.base/java.lang.reflect.Method.invoke(Method.java:565) ~[na:na]
        at org.springframework.web.method.support.InvocableHandlerMethod.doInvoke(InvocableHandlerMethod.java:258) ~[spring-web-6.2.7.jar:6.2.7]
        at org.springframework.web.method.support.InvocableHandlerMethod.invokeForRequest(InvocableHandlerMethod.java:191) ~[spring-web-6.2.7.jar:6.2.7]
        at org.springframework.web.servlet.mvc.method.annotation.ServletInvocableHandlerMethod.invokeAndHandle(ServletInvocableHandlerMethod.java:118) ~[spring-webmvc-6.2.7.jar:6.2.7]
        at org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter.invokeHandlerMethod(RequestMappingHandlerAdapter.java:986) ~[spring-webmvc-6.2.7.jar:6.2.7]
        at org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter.handleInternal(RequestMappingHandlerAdapter.java:891) ~[spring-webmvc-6.2.7.jar:6.2.7]
        at org.springframework.web.servlet.mvc.method.AbstractHandlerMethodAdapter.handle(AbstractHandlerMethodAdapter.java:87) ~[spring-webmvc-6.2.7.jar:6.2.7]
        at org.springframework.web.servlet.DispatcherServlet.doDispatch(DispatcherServlet.java:1089) ~[spring-webmvc-6.2.7.jar:6.2.7]
        at org.springframework.web.servlet.DispatcherServlet.doService(DispatcherServlet.java:979) ~[spring-webmvc-6.2.7.jar:6.2.7]
        at org.springframework.web.servlet.FrameworkServlet.processRequest(FrameworkServlet.java:1014) ~[spring-webmvc-6.2.7.jar:6.2.7]
        at org.springframework.web.servlet.FrameworkServlet.doGet(FrameworkServlet.java:903) ~[spring-webmvc-6.2.7.jar:6.2.7]
        at jakarta.servlet.http.HttpServlet.service(HttpServlet.java:564) ~[tomcat-embed-core-10.1.41.jar:6.0]
        at org.springframework.web.servlet.FrameworkServlet.service(FrameworkServlet.java:885) ~[spring-webmvc-6.2.7.jar:6.2.7]
        at jakarta.servlet.http.HttpServlet.service(HttpServlet.java:658) ~[tomcat-embed-core-10.1.41.jar:6.0]
        at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:195) ~[tomcat-embed-core-10.1.41.jar:10.1.41]
        at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:140) ~[tomcat-embed-core-10.1.41.jar:10.1.41]
        at org.apache.tomcat.websocket.server.WsFilter.doFilter(WsFilter.java:51) ~[tomcat-embed-websocket-10.1.41.jar:10.1.41]
        at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:164) ~[tomcat-embed-core-10.1.41.jar:10.1.41]
        at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:140) ~[tomcat-embed-core-10.1.41.jar:10.1.41]
        at org.springframework.web.filter.RequestContextFilter.doFilterInternal(RequestContextFilter.java:100) ~[spring-web-6.2.7.jar:6.2.7]
        at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:116) ~[spring-web-6.2.7.jar:6.2.7]
        at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:164) ~[tomcat-embed-core-10.1.41.jar:10.1.41]
        at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:140) ~[tomcat-embed-core-10.1.41.jar:10.1.41]
        at org.springframework.web.filter.FormContentFilter.doFilterInternal(FormContentFilter.java:93) ~[spring-web-6.2.7.jar:6.2.7]
        at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:116) ~[spring-web-6.2.7.jar:6.2.7]
        at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:164) ~[tomcat-embed-core-10.1.41.jar:10.1.41]
        at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:140) ~[tomcat-embed-core-10.1.41.jar:10.1.41]
        at org.springframework.web.filter.ServerHttpObservationFilter.doFilterInternal(ServerHttpObservationFilter.java:114) ~[spring-web-6.2.7.jar:6.2.7]
        at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:116) ~[spring-web-6.2.7.jar:6.2.7]
        at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:164) ~[tomcat-embed-core-10.1.41.jar:10.1.41]
        at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:140) ~[tomcat-embed-core-10.1.41.jar:10.1.41]
        at org.springframework.web.filter.CharacterEncodingFilter.doFilterInternal(CharacterEncodingFilter.java:201) ~[spring-web-6.2.7.jar:6.2.7]
        at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:116) ~[spring-web-6.2.7.jar:6.2.7]
        at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:164) ~[tomcat-embed-core-10.1.41.jar:10.1.41]
        at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:140) ~[tomcat-embed-core-10.1.41.jar:10.1.41]
        at org.apache.catalina.core.StandardWrapperValve.invoke(StandardWrapperValve.java:167) ~[tomcat-embed-core-10.1.41.jar:10.1.41]
        at org.apache.catalina.core.StandardContextValve.invoke(StandardContextValve.java:90) ~[tomcat-embed-core-10.1.41.jar:10.1.41]
        at org.apache.catalina.authenticator.AuthenticatorBase.invoke(AuthenticatorBase.java:483) ~[tomcat-embed-core-10.1.41.jar:10.1.41]
        at org.apache.catalina.core.StandardHostValve.invoke(StandardHostValve.java:116) ~[tomcat-embed-core-10.1.41.jar:10.1.41]
        at org.apache.catalina.valves.ErrorReportValve.invoke(ErrorReportValve.java:93) ~[tomcat-embed-core-10.1.41.jar:10.1.41]
        at org.apache.catalina.core.StandardEngineValve.invoke(StandardEngineValve.java:74) ~[tomcat-embed-core-10.1.41.jar:10.1.41]
        at org.apache.catalina.connector.CoyoteAdapter.service(CoyoteAdapter.java:344) ~[tomcat-embed-core-10.1.41.jar:10.1.41]
        at org.apache.coyote.http11.Http11Processor.service(Http11Processor.java:398) ~[tomcat-embed-core-10.1.41.jar:10.1.41]
        at org.apache.coyote.AbstractProcessorL
