2025-10-11T10:35:42.840-05:00  INFO 28892 --- [client-sysprin-reader] [0.0-8083-exec-1] rapid.client.web.ClientController        : request client details: page 0 size 20
2025-10-11T10:35:43.451-05:00 ERROR 28892 --- [client-sysprin-reader] [0.0-8083-exec-1] o.a.c.c.C.[.[.[/].[dispatcherServlet]    : Servlet.service() for servlet [dispatcherServlet] in context with path [] threw exception [Request processing failed: org.springframework.jdbc.UncategorizedSQLException: PreparedStatementCallback; uncategorized SQLException for SQL [WITH page_clients AS (
  SELECT
    c.client,
    c.name,
    c.addr,
    c.city,
    c.state,
    c.zip,
    c.contact,
    c.phone,
    c.active,
    c.fax_number,
    c.billing_sp,
    c.report_break_flag,
    c.chlookup_type,
    c.exclude_from_report,
    c.positive_reports,
    c.sub_client_ind,
    c.sub_client_xref,
    c.amex_issued
  FROM clients c
  WHERE c.client IS NOT NULL
    AND c.client <> ''
    AND c.name   <> ''
  ORDER BY c.client
  OFFSET (@page * @size) ROWS FETCH NEXT @size ROWS ONLY
) SELECT (
  SELECT
    pc.client               AS [client],
    pc.name                 AS [name],
    pc.addr                 AS [addr],
    pc.city                 AS [city],
    pc.state                AS [state],
    pc.zip                  AS [zip],
    pc.contact              AS [contact],
    pc.phone                AS [phone],
    pc.active               AS [active],
    pc.fax_number           AS [faxNumber],
    pc.billing_sp           AS [billingSp],
    pc.report_break_flag    AS [reportBreakFlag],
    pc.chlookup_type        AS [chLookUpType],
    pc.exclude_from_report  AS [excludeFromReport],
    pc.positive_reports     AS [positiveReports],
    pc.sub_client_ind       AS [subClientInd],
    pc.sub_client_xref      AS [subClientXref],
    pc.amex_issued          AS [amexIssued],

    /* ---------- reportOptions (array) ---------- */
    JSON_QUERY(ro_json.reportOptions) AS [reportOptions],

    /* ---------- sysPrinsPrefixes (array) ---------- */
    JSON_QUERY(spp_json.sysPrinsPrefixes) AS [sysPrinsPrefixes],

    /* ---------- clientEmail (array) ---------- */
    JSON_QUERY(ce_json.clientEmail) AS [clientEmail],

    /* ---------- sysPrins (array) ---------- */
    JSON_QUERY(sp_json.sysPrins) AS [sysPrins]

  FROM page_clients pc

  /* Build reportOptions in one pass (JOINs, no nested scalar lookups) */
  OUTER APPLY (
    SELECT
      (
        SELECT
          ro.client_id       AS [clientId],
          ro.report_id       AS [reportId],
          ro.receive_flag    AS [receiveFlag],
          ro.output_type_cd  AS [outputTypeCd],
          ro.file_type_cd    AS [fileTypeCd],
          ro.email_flag      AS [emailFlag],
          ro.email_body_tx   AS [emailBodyTx],
          ro.report_password_tx AS [reportPasswordTx],

          /* reportDetails (single object, assembled via JOINs) */
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
                  ft.file_trns_id     AS [fileTransId],
                  ft.sequence_nr      AS [sequenceNr],
                  ft.transfer_cd      AS [transferCd],
                  ft.protocol_nm      AS [protocolNm],
                  ft.trans_prg_nm     AS [transPrgNm],
                  ft.ip_port_cd       AS [ipPortCd],
                  ft.block_size_nr    AS [blockSizeNr],
                  ft.convert_file_cd  AS [convertFileCd],
                  ft.mode_nm          AS [modeNm],
                  ft.security_nm      AS [securityNm],
                  ft.xfer_file_nm     AS [xferFileNm],
                  ft.dd_nm            AS [ddNm],
                  ft.member_cd        AS [memberCd],
                  ft.job_nm           AS [jobNm],
                  ft.remote_file_nm   AS [remoteFileNm],
                  ft.gateway_access_cd AS [gatewayAccessCd],
                  ft.listener_srv_nm  AS [listenerSrvNm],
                  ft.org_type_cd      AS [orgTypeCd],
                  ft.program_nm       AS [programNm],
                  ft.bin_file_CRLF_ind AS [binFileCRLFInf],
                  ft.control_file_nm  AS [controlFileNm],
                  ft.record_lgth_nr   AS [recordLengthNr],
                  ft.local_file_nm    AS [localFileNm]
                FROM c3_transfer_parameters ft
                WHERE ft.file_trns_id = rd.report_id
                FOR JSON PATH, WITHOUT_ARRAY_WRAPPER
              )) AS [c3FileTransfer]
            FROM ADMIN_QUERY_LIST rd
            WHERE rd.report_id = ro.report_id
            FOR JSON PATH, WITHOUT_ARRAY_WRAPPER
          )) AS [reportDetails]
        FROM CLIENT_REPORT_OPTIONS ro
        WHERE ro.client_id = pc.client
        -- Avoid ORDER BY here unless the UI depends on it
        FOR JSON PATH
      ) AS reportOptions
  ) ro_json

  /* sys_prins_prefix for the page's billing_sp */
  OUTER APPLY (
    SELECT (
      SELECT
        spp.billing_sp   AS [billingSp],
        spp.prefix       AS [prefix],
        spp.atm_cash_rule AS [atmCashRule]
      FROM sys_prins_prefix spp
      WHERE spp.billing_sp = pc.billing_sp
      FOR JSON PATH
    ) AS sysPrinsPrefixes
  ) spp_json

  /* client_email for the page's clients */
  OUTER APPLY (
    SELECT (
      SELECT
        ce.client_id        AS [clientId],
        ce.email_address_tx AS [emailAddressTx],
        ce.report_id        AS [reportId],
        ce.email_name_tx    AS [emailNameTx],
        ce.carbon_copy_flag AS [carbonCopyFlag],
        ce.active_flag      AS [activeFlag],
        ce.mail_server_id   AS [mailServerId]
      FROM CLIENT_EMAIL ce
      WHERE ce.client_id = pc.client
      FOR JSON PATH
    ) AS clientEmail
  ) ce_json

  /* sys_prins (and nested arrays) for the page's clients */
  OUTER APPLY (
    SELECT (
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
        sp.active          AS [active],
        sp.notes           AS [notes],
        sp.RET_STAT        AS [returnStatus],
        sp.DES_STAT        AS [destroyStatus],
        sp.non_us          AS [nonUS],
        sp.special         AS [special],
        sp.pin             AS [pinMailer],
        sp.hold_days       AS [holdDays],
        sp.FORWARDING_ADDR AS [forwardingAddress],
        sp.contact         AS [contact],
        sp.phone           AS [phone],
        sp.ENTITY_CD       AS [entityCode],

        /* invalidDelivAreas */
        JSON_QUERY((
          SELECT
            ida.area     AS [area],
            ida.sys_prin AS [sysPrin]
          FROM invalid_deliv_areas ida
          WHERE ida.sys_prin = sp.sys_prin
          FOR JSON PATH
        )) AS [invalidDelivAreas],

        /* vendorSentTo: JOIN vendor and filter IO='I' (no EXISTS) */
        JSON_QUERY((
          SELECT
            vst.sys_prin      AS [sysPrin],
            vst.vend_id       AS [vendorId],
            vst.queformail_cd AS [queForMail],
            JSON_QUERY((
              SELECT
                v.vend_id              AS [id],
                v.vend_nm              AS [name],
                v.vend_actv_cd         AS [active],
                v.vend_rcvr_cd         AS [receiver],
                v.vend_fsrv_nm         AS [fileServerName],
                v.vend_fsrv_ip         AS [fileServerIp],
                v.fsrvr_user_id        AS [fileServerUserId],
                v.fsrvr_usr_pwd_tx     AS [fileServerPassword],
                v.fsrvr_file_name_tx   AS [fileName],
                v.fsrvr_unc_share_tx   AS [uncShare],
                v.fsrvr_path_tx        AS [path],
                v.fsrvr_file_archive_path_tx AS [archivePath],
                v.vendor_type_cd       AS [vendorTypeCode],
                v.vend_file_io         AS [fileIo],
                v.vend_client_specific AS [clientSpecific],
                v.vend_schedule        AS [schedule],
                v.vend_date_last_worked AS [dateLastWorked],
                v.vend_filesize        AS [fileSize],
                v.vend_filetrans_specs AS [fileTransferSpecs],
                v.vend_file_type       AS [fileType],
                v.ftp_passive          AS [ftpPassive],
                v.ftp_filetype         AS [ftpFileType]
              FOR JSON PATH, WITHOUT_ARRAY_WRAPPER
            )) AS [vendor]
          FROM vendor_sent_to vst
          JOIN VENDOR v ON v.vend_id = vst.vend_id
                        AND v.vend_file_io = 'I'
          WHERE vst.sys_prin = sp.sys_prin
          FOR JSON PATH
        )) AS [vendorSentTo],

        /* vendorReceivedFrom: JOIN vendor and filter IO='O' */
        JSON_QUERY((
          SELECT
            vrf.sys_prin      AS [sysPrin],
            vrf.vend_id       AS [vendorId],
            vrf.queformail_cd AS [queForMail],
            JSON_QUERY((
              SELECT
                v.vend_id              AS [id],
                v.vend_nm              AS [name],
                v.vend_actv_cd         AS [active],
                v.vend_rcvr_cd         AS [receiver],
                v.vend_fsrv_nm         AS [fileServerName],
                v.vend_fsrv_ip         AS [fileServerIp],
                v.fsrvr_user_id        AS [fileServerUserId],
                v.fsrvr_usr_pwd_tx     AS [fileServerPassword],
                v.fsrvr_file_name_tx   AS [fileName],
                v.fsrvr_unc_share_tx   AS [uncShare],
                v.fsrvr_path_tx        AS [path],
                v.fsrvr_file_archive_path_tx AS [archivePath],
                v.vendor_type_cd       AS [vendorTypeCode],
                v.vend_file_io         AS [fileIo],
                v.vend_client_specific AS [clientSpecific],
                v.vend_schedule        AS [schedule],
                v.vend_date_last_worked AS [dateLastWorked],
                v.vend_filesize        AS [fileSize],
                v.vend_filetrans_specs AS [fileTransferSpecs],
                v.vend_file_type       AS [fileType],
                v.ftp_passive          AS [ftpPassive],
                v.ftp_filetype         AS [ftpFileType]
              FOR JSON PATH, WITHOUT_ARRAY_WRAPPER
            )) AS [vendor]
          FROM vendor_sent_to vrf
          JOIN VENDOR v ON v.vend_id = vrf.vend_id
                        AND v.vend_file_io = 'O'
          WHERE vrf.sys_prin = sp.sys_prin
          FOR JSON PATH
        )) AS [vendorReceivedFrom]

      FROM sys_prins sp
      WHERE sp.client = pc.client
      FOR JSON PATH
    ) AS sysPrins
  ) sp_json

  FOR JSON PATH
) AS full_json;]; SQL state [S0002]; error code [137]; Must declare the scalar variable "@page".] with root cause

com.microsoft.sqlserver.jdbc.SQLServerException: Must declare the scalar variable "@page".
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
        at rapid.repository.client.ClientDetailDaoWithNativeSql.fetchClientDetailPageWithNativeSql(ClientDetailDaoWithNativeSql.java:24) ~[common-model-0.0.1-SNAPSHOT.jar:na]
        at java.base/jdk.internal.reflect.DirectMethodHandleAccessor.invoke(DirectMethodHandleAccessor.java:104) ~[na:na]
        at java.base/java.lang.reflect.Method.invoke(Method.java:565) ~[na:na]
        at org.springframework.aop.support.AopUtils.invokeJoinpointUsingReflection(AopUtils.java:359) ~[spring-aop-6.2.7.jar:6.2.7]
        at org.springframework.aop.framework.ReflectiveMethodInvocation.invokeJoinpoint(ReflectiveMethodInvocation.java:196) ~[spring-aop-6.2.7.jar:6.2.7]
        at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:163) ~[spring-aop-6.2.7.jar:6.2.7]
        at org.springframework.dao.support.PersistenceExceptionTranslationInterceptor.invoke(PersistenceExceptionTranslationInterceptor.java:138) ~[spring-tx-6.2.7.jar:6.2.7]
        at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:184) ~[spring-aop-6.2.7.jar:6.2.7]
        at org.springframework.aop.framework.CglibAopProxy$DynamicAdvisedInterceptor.intercept(CglibAopProxy.java:728) ~[spring-aop-6.2.7.jar:6.2.7]
        at rapid.repository.client.ClientDetailDaoWithNativeSql$$SpringCGLIB$$0.fetchClientDetailPageWithNativeSql(<generated>) ~[common-model-0.0.1-SNAPSHOT.jar:na]
        at rapid.service.client.ClientNativeSqlService.fetchClientDetailJsonPage(ClientNativeSqlService.java:16) ~[common-services-0.0.1-SNAPSHOT.jar:na]
        at rapid.client.web.ClientController.getClientsJson(ClientController.java:45) ~[classes/:na]
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
