2026-01-14T15:34:46.024-06:00 ERROR 36520 --- [clients] [0.0-8081-exec-1] o.a.c.c.C.[.[.[/].[dispatcherServlet]    : Servlet.service() for servlet [dispatcherServlet] in context with path [] threw exception [Request processing failed: org.springframework.jdbc.UncategorizedSQLException: PreparedStatementCallback; uncategorized SQLException for SQL [SELECT ISNULL((
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
    (
      SELECT ida.area AS [area], ida.sys_prin AS [sysPrin]
      FROM invalid_deliv_areas ida
      WHERE ida.sys_prin = sp.sys_prin
      ORDER BY ida.area
      FOR JSON PATH, INCLUDE_NULL_VALUES
    ) AS [invalidDelivAreas],

    /* ? Add this count for sysprin vendor */

    (SELECT COUNT_BIG(*)
     FROM Vendor v
     WHERE v.Vend_file_IO = 'I' and v.Vend_Actv_cd = 1
       AND NOT EXISTS (
           SELECT 1
           FROM Vendor_Sent_to vst
           WHERE vst.sys_prin = sp.sys_prin
             AND vst.vend_id  = v.vend_id
       )) AS [vendorForSentToTotal],

    /* vendorSentTo (I) */
   JSON_QUERY((
      SELECT
        x.sys_prin      AS [sysPrin],
        x.vend_id       AS [vendorId],
        x.queformail_cd AS [queForMail],
        JSON_QUERY(vj.vendorJson) AS [vendor]
      FROM (
            SELECT
              vrf.sys_prin,
              vrf.vend_id,
              vrf.queformail_cd,
              ROW_NUMBER() OVER (
                PARTITION BY vrf.sys_prin
                ORDER BY vrf.vend_id
              ) AS rn
            FROM vendor_sent_to vrf
            WHERE vrf.sys_prin = sp.sys_prin
              AND EXISTS (
                SELECT 1
                FROM vendor vv
                WHERE vv.vend_id = vrf.vend_id
                  AND vv.vend_file_io = 'I'
              )
           ) x
      CROSS APPLY (
        SELECT
          (
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
            FROM vendor v
            WHERE v.vend_id = x.vend_id
              AND v.vend_file_io = 'I'
            FOR JSON PATH, WITHOUT_ARRAY_WRAPPER, INCLUDE_NULL_VALUES
          ) AS vendorJson
      ) vj
      WHERE x.rn <= 10
      ORDER BY x.vend_id
      FOR JSON PATH, INCLUDE_NULL_VALUES
    )) AS [vendorSentTo],


/* ? Add this count for sysprin vendor */
    (SELECT COUNT_BIG(*)
     FROM Vendor v
     WHERE v.Vend_file_IO = 'O' and v.Vend_Actv_cd = 1
       AND NOT EXISTS (
           SELECT 1
           FROM Vendor_Sent_to vst
           WHERE vst.sys_prin = sp.sys_prin
             AND vst.vend_id  = v.vend_id
       )) AS [vendorForReceivedFromTotal],

   /* vendorReceivedFrom count (per sys_prin TOP 10 only; vendors with IO='O') */
   (
     SELECT COUNT(*)
     FROM (
       SELECT
         vrf.sys_prin,
         vrf.vend_id,
         vrf.queformail_cd,
         ROW_NUMBER() OVER (
           PARTITION BY vrf.sys_prin
         ) AS rn
       FROM vendor_sent_to vrf
       WHERE vrf.sys_prin = sp.sys_prin
         AND EXISTS (
           SELECT 1
           FROM vendor vv
           WHERE vv.vend_id = vrf.vend_id
             AND vv.vend_file_io = 'O'
         )
     ) x
   ) AS [vendorReceivedFromTotal],

    /* vendorReceivedFrom (O) */
    JSON_QUERY((
      SELECT
        x.sys_prin      AS [sysPrin],
        x.vend_id       AS [vendorId],
        x.queformail_cd AS [queForMail],
        JSON_QUERY(vj.vendorJson) AS [vendor]
      FROM (
            SELECT
              vrf.sys_prin,
              vrf.vend_id,
              vrf.queformail_cd,
              ROW_NUMBER() OVER (
                PARTITION BY vrf.sys_prin
                ORDER BY vrf.vend_id
              ) AS rn
            FROM vendor_sent_to vrf
            WHERE vrf.sys_prin = sp.sys_prin
              AND EXISTS (
                SELECT 1
                FROM vendor vv
                WHERE vv.vend_id = vrf.vend_id
                  AND vv.vend_file_io = 'O'
              )
           ) x
      CROSS APPLY (
        SELECT
          (
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
            FROM vendor v
            WHERE v.vend_id = x.vend_id
              AND v.vend_file_io = 'O'
            FOR JSON PATH, WITHOUT_ARRAY_WRAPPER, INCLUDE_NULL_VALUES
          ) AS vendorJson
      ) vj
      WHERE x.rn <= 10
      ORDER BY x.vend_id
      FOR JSON PATH, INCLUDE_NULL_VALUES
    )) AS [vendorReceivedFrom]

  FROM sys_prins sp
  WHERE sp.client = ?
    AND sp.sys_prin = ?
  FOR JSON PATH, INCLUDE_NULL_VALUES
), N'[]') AS full_json;]; SQL state [S0001]; error code [4112]; The function 'ROW_NUMBER' must have an OVER clause with ORDER BY.] with root cause

com.microsoft.sqlserver.jdbc.SQLServerException: The function 'ROW_NUMBER' must have an OVER clause with ORDER BY.
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
        at trace.repository.sysprin.SysPrinDetailDaoWithNativeSql.fetchSysPrinDetailFullJsonPage(SysPrinDetailDaoWithNativeSql.java:32) ~[trace-common-model-1.0.0-SNAPSHOT.jar:na]
