SELECT
            (
              SELECT
                  c.client            AS [client],
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

                  /* ---------- reportOptions (array) ---------- */
                  JSON_QUERY((
                    SELECT
                      ro.client_id         AS [clientId],
                      ro.report_id         AS [reportId],
                      ro.receive_flag      AS [receiveFlag],
                      ro.output_type_cd    AS [outputTypeCd],
                      ro.file_type_cd      AS [fileTypeCd],
                      ro.email_flag        AS [emailFlag],
                      ro.email_body_tx     AS [emailBodyTx],
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
                              ft.file_trns_id     AS [fileTransId],
                              ft.sequence_nr      AS [sequenceNr],
                              ft.transfer_cd      AS [transferCd],
                              ft.protocol_nm      AS [protocolNm],
                              ft.trans_prg_nm     AS [transPrgNm],
                              ft.ip_port_cd       AS [ipPortCd],
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
                    -- (optional) impose stable order for array:
                    ORDER BY ro.report_id OFFSET 0 ROWS
                    FOR JSON PATH
                  )) AS [reportOptions],

                  /* ---------- sysPrinsPrefixes (array) ---------- */
                  JSON_QUERY((
                    SELECT
                      spp.billing_sp   AS [billingSp],
                      spp.prefix       AS [prefix],
                      spp.atm_cash_rule AS [atmCashRule]
                    FROM sys_prins_prefix spp
                    WHERE spp.BILLING_SP = c.billing_sp
                    ORDER BY spp.prefix OFFSET 0 ROWS
                    FOR JSON PATH
                  )) AS [sysPrinsPrefixes],

                  /* ---------- clientEmail (array) ---------- */
                  JSON_QUERY((
                    SELECT
                      ce.client_id       AS [clientId],
                      ce.email_address_tx AS [emailAddressTx],
                      ce.report_id       AS [reportId],
                      ce.email_name_tx   AS [emailNameTx],
                      ce.carbon_copy_flag AS [carbonCopyFlag],
                      ce.active_flag     AS [activeFlag],
                      ce.mail_server_id  AS [mailServerId]
                    FROM CLIENT_EMAIL ce
                    WHERE ce.client_id = c.client
                    ORDER BY ce.report_id, ce.email_address_tx OFFSET 0 ROWS
                    FOR JSON PATH
                  )) AS [clientEmail],

                  /* ---------- sysPrins (array) ---------- */
                  JSON_QUERY((
                    SELECT
                      sp.client             AS [client],
                      sp.sys_prin           AS [sysPrin],
                      sp.cust_type          AS [custType],
                      sp.undeliverable      AS [undeliverable],
                      sp.stat_a             AS [statA],
                      sp.stat_b             AS [statB],
                      sp.stat_c             AS [statC],
                      sp.stat_d             AS [statD],
                      sp.stat_e             AS [statE],
                      sp.stat_f             AS [statF],
                      sp.stat_i             AS [statI],
                      sp.stat_l             AS [statL],
                      sp.stat_o             AS [statO],
                      sp.stat_u             AS [statU],
                      sp.stat_x             AS [statX],
                      sp.stat_z             AS [statZ],
                      sp.po_box             AS [poBox],
                      sp.addr_flag          AS [addrFlag],
                      sp.temp_away          AS [tempAway],
                      sp.rps                AS [rps],
                      sp.session            AS [session],
                      sp.bad_state          AS [badState],
                      sp.A_STAT_RCH         AS [astatRch],
                      sp.NM_13              AS [nm13],
                      sp.temp_away_atts     AS [tempAwayAtts],
                      sp.report_method      AS [reportMethod],
                      sp.active             AS [active],
                      sp.notes              AS [notes],
                      sp.RET_STAT           AS [returnStatus],
                      sp.DES_STAT           AS [destroyStatus],
                      sp.non_us             AS [nonUS],
                      sp.special            AS [special],
                      sp.pin                AS [pinMailer],
                      sp.hold_days          AS [holdDays],
                      sp.FORWARDING_ADDR    AS [forwardingAddress],
                      sp.contact            AS [contact],
                      sp.phone              AS [phone],
                      sp.ENTITY_CD          AS [entityCode],

                      /* invalidDelivAreas (array) */
                      JSON_QUERY((
                        SELECT
                          ida.area     AS [area],
                          ida.sys_prin AS [sysPrin]
                        FROM invalid_deliv_areas ida
                        WHERE ida.sys_prin = sp.sys_prin
                        FOR JSON PATH
                      )) AS [invalidDelivAreas],

                      /* vendorSentTo (array; only vendors with IO='I') */
                      JSON_QUERY((
                        SELECT
                          vst.sys_prin     AS [sysPrin],
                          vst.vend_id      AS [vendorId],
                          vst.queformail_cd AS [queForMail],
                          JSON_QUERY((
                            SELECT
                              v.vend_id            AS [id],
                              v.vend_nm            AS [name],
                              v.vend_actv_cd       AS [active],
                              v.vend_rcvr_cd       AS [receiver],
                              v.vend_fsrv_nm       AS [fileServerName],
                              v.vend_fsrv_ip       AS [fileServerIp],
                              v.fsrvr_user_id      AS [fileServerUserId],
                              v.fsrvr_usr_pwd_tx   AS [fileServerPassword],
                              v.fsrvr_file_name_tx AS [fileName],
                              v.fsrvr_unc_share_tx AS [uncShare],
                              v.fsrvr_path_tx      AS [path],
                              v.fsrvr_file_archive_path_tx AS [archivePath],
                              v.vendor_type_cd     AS [vendorTypeCode],
                              v.vend_file_io       AS [fileIo],
                              v.vend_client_specific AS [clientSpecific],
                              v.vend_schedule      AS [schedule],
                              v.vend_date_last_worked AS [dateLastWorked],
                              v.vend_filesize      AS [fileSize],
                              v.vend_filetrans_specs AS [fileTransferSpecs],
                              v.vend_file_type     AS [fileType],
                              v.ftp_passive        AS [ftpPassive],
                              v.ftp_filetype       AS [ftpFileType]
                            FROM VENDOR v
                            WHERE v.vend_id = vst.vend_id
                            FOR JSON PATH, WITHOUT_ARRAY_WRAPPER
                          )) AS [vendor]
                        FROM vendor_sent_to vst
                        WHERE vst.sys_prin = sp.sys_prin
                          AND EXISTS (
                            SELECT 1 FROM VENDOR vv
                            WHERE vv.vend_id = vst.vend_id AND vv.vend_file_io = 'I'
                          )
                        FOR JSON PATH
                      )) AS [vendorSentTo],

                      /* vendorReceivedFrom (array; only vendors with IO='O') */
                      JSON_QUERY((
                        SELECT
                          vrf.sys_prin     AS [sysPrin],
                          vrf.vend_id      AS [vendorId],
                          vrf.queformail_cd AS [queForMail],
                          JSON_QUERY((
                            SELECT
                              v.vend_id            AS [id],
                              v.vend_nm            AS [name],
                              v.vend_actv_cd       AS [active],
                              v.vend_rcvr_cd       AS [receiver],
                              v.vend_fsrv_nm       AS [fileServerName],
                              v.vend_fsrv_ip       AS [fileServerIp],
                              v.fsrvr_user_id      AS [fileServerUserId],
                              v.fsrvr_usr_pwd_tx   AS [fileServerPassword],
                              v.fsrvr_file_name_tx AS [fileName],
                              v.fsrvr_unc_share_tx AS [uncShare],
                              v.fsrvr_path_tx      AS [path],
                              v.fsrvr_file_archive_path_tx AS [archivePath],
                              v.vendor_type_cd     AS [vendorTypeCode],
                              v.vend_file_io       AS [fileIo],
                              v.vend_client_specific AS [clientSpecific],
                              v.vend_schedule      AS [schedule],
                              v.vend_date_last_worked AS [dateLastWorked],
                              v.vend_filesize      AS [fileSize],
                              v.vend_filetrans_specs AS [fileTransferSpecs],
                              v.vend_file_type     AS [fileType],
                              v.ftp_passive        AS [ftpPassive],
                              v.ftp_filetype       AS [ftpFileType]
                            FROM VENDOR v
                            WHERE v.vend_id = vrf.vend_id AND v.vend_file_io = 'O'
                            FOR JSON PATH, WITHOUT_ARRAY_WRAPPER
                          )) AS [vendor]
                        FROM vendor_sent_to vrf
                        WHERE vrf.sys_prin = sp.sys_prin
                          AND EXISTS (
                            SELECT 1 FROM VENDOR vv
                            WHERE vv.vend_id = vrf.vend_id AND vv.vend_file_io = 'O'
                          )
                        FOR JSON PATH
                      )) AS [vendorReceivedFrom]

                    FROM sys_prins sp
                    WHERE sp.client = c.client
                    FOR JSON PATH
                  )) AS [sysPrins]

              FROM clients c
              WHERE c.client IS NOT NULL and client != '' and name != ''
              ORDER BY c.client
              OFFSET (:page * :size) ROWS FETCH NEXT :size ROWS ONLY
              FOR JSON PATH
            ) AS full_json;



DECLARE @page   int = 2;   -- hardcode here
DECLARE @size   int = 25;  -- hardcode here
DECLARE @offset int = @page * @size;

...
ORDER BY c.client
OFFSET @offset ROWS FETCH NEXT @size ROWS ONLY
...








            SET NOCOUNT ON;

DECLARE @page   int  = :page;
DECLARE @size   int  = :size;
DECLARE @offset int  = @page * @size;

/* Seek boundary: find the last client of the previous page.
   This replaces large OFFSET scans with an index-friendly seek. */
DECLARE @lastClient nvarchar(100);
SELECT @lastClient = MAX(c.client)
FROM (
  SELECT TOP (@offset) c.client
  FROM clients c
  WHERE c.client IS NOT NULL AND c.client <> '' AND c.name <> ''
  ORDER BY c.client
) t;

/* Materialize page clients once (tiny working set). Use #temp or @table.
   (If you use @table, you can declare a PRIMARY KEY to help joins.) */
CREATE TABLE #page_clients (
  client           nvarchar(100) PRIMARY KEY,
  name             nvarchar(200),
  addr             nvarchar(200),
  city             nvarchar(100),
  state            nvarchar(50),
  zip              nvarchar(20),
  contact          nvarchar(200),
  phone            nvarchar(50),
  active           bit,
  fax_number       nvarchar(50),
  billing_sp       nvarchar(50),
  report_break_flag bit,
  chlookup_type    nvarchar(50),
  exclude_from_report bit,
  positive_reports bit,
  sub_client_ind   bit,
  sub_client_xref  nvarchar(100),
  amex_issued      bit
);

INSERT #page_clients (client,name,addr,city,state,zip,contact,phone,active,fax_number,
                      billing_sp,report_break_flag,chlookup_type,exclude_from_report,
                      positive_reports,sub_client_ind,sub_client_xref,amex_issued)
SELECT TOP (@size)
  c.client, c.name, c.addr, c.city, c.state, c.zip, c.contact, c.phone, c.active,
  c.fax_number, c.billing_sp, c.report_break_flag, c.chlookup_type, c.exclude_from_report,
  c.positive_reports, c.sub_client_ind, c.sub_client_xref, c.amex_issued
FROM clients c
WHERE c.client IS NOT NULL AND c.client <> '' AND c.name <> ''
  AND (@lastClient IS NULL OR c.client > @lastClient)  -- seek, not skip
ORDER BY c.client;

/* Optional: pre-materialize sys_prins for the page; it’s often the biggest fan-out. */
CREATE TABLE #sp (
  client   nvarchar(100),
  sys_prin nvarchar(100),
  cust_type nvarchar(50),
  undeliverable bit,
  stat_a bit, stat_b bit, stat_c bit, stat_d bit, stat_e bit, stat_f bit,
  stat_i bit, stat_l bit, stat_o bit, stat_u bit, stat_x bit, stat_z bit,
  po_box nvarchar(100),
  addr_flag bit, temp_away bit, rps nvarchar(50), session nvarchar(50),
  bad_state bit, A_STAT_RCH nvarchar(50), NM_13 nvarchar(50),
  temp_away_atts nvarchar(200), report_method nvarchar(50), active bit,
  notes nvarchar(max), RET_STAT nvarchar(50), DES_STAT nvarchar(50),
  non_us bit, special bit, pin bit, hold_days int, FORWARDING_ADDR nvarchar(200),
  contact nvarchar(200), phone nvarchar(50), ENTITY_CD nvarchar(50)
);

INSERT #sp
SELECT sp.*
FROM sys_prins sp
JOIN #page_clients pc ON pc.client = sp.client;

/* Now build JSON using only page data */
SELECT (
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

    /* reportOptions (array) */
    JSON_QUERY((
      SELECT
        ro.client_id       AS [clientId],
        ro.report_id       AS [reportId],
        ro.receive_flag    AS [receiveFlag],
        ro.output_type_cd  AS [outputTypeCd],
        ro.file_type_cd    AS [fileTypeCd],
        ro.email_flag      AS [emailFlag],
        ro.email_body_tx   AS [emailBodyTx],
        ro.report_password_tx AS [reportPasswordTx],
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
      JOIN #page_clients pc2 ON pc2.client = ro.client_id
      FOR JSON PATH
    )) AS [reportOptions],

    /* sysPrinsPrefixes (array) */
    JSON_QUERY((
      SELECT
        spp.billing_sp   AS [billingSp],
        spp.prefix       AS [prefix],
        spp.atm_cash_rule AS [atmCashRule]
      FROM sys_prins_prefix spp
      JOIN #page_clients pc3 ON pc3.billing_sp = spp.billing_sp
      WHERE pc3.client = pc.client
      FOR JSON PATH
    )) AS [sysPrinsPrefixes],

    /* clientEmail (array) */
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
      WHERE ce.client_id = pc.client
      FOR JSON PATH
    )) AS [clientEmail],

    /* sysPrins (array) — from pre-materialized #sp */
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
          SELECT ida.area AS [area], ida.sys_prin AS [sysPrin]
          FROM invalid_deliv_areas ida
          WHERE ida.sys_prin = sp.sys_prin
          FOR JSON PATH
        )) AS [invalidDelivAreas],

        /* vendorSentTo (IO='I') */
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
          JOIN VENDOR v ON v.vend_id = vst.vend_id AND v.vend_file_io = 'I'
          WHERE vst.sys_prin = sp.sys_prin
          FOR JSON PATH
        )) AS [vendorSentTo],

        /* vendorReceivedFrom (IO='O') */
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
          JOIN VENDOR v ON v.vend_id = vrf.vend_id AND v.vend_file_io = 'O'
          WHERE vrf.sys_prin = sp.sys_prin
          FOR JSON PATH
        )) AS [vendorReceivedFrom]

      FROM #sp sp
      WHERE sp.client = pc.client
      FOR JSON PATH
    )) AS [sysPrins]

  FROM #page_clients pc
  ORDER BY pc.client
  FOR JSON PATH
) AS full_json
OPTION (RECOMPILE);


