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
        OFFSET :offset ROWS FETCH NEXT :size ROWS ONLY
        FOR JSON PATH
      ) AS full_json
