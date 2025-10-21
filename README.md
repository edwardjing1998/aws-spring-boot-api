    fetchSysPrinDetailFullJson: >-
      SELECT ISNULL((
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
          ( SELECT ida.area AS [area], ida.sys_prin AS [sysPrin]
            FROM invalid_deliv_areas ida
            WHERE ida.sys_prin = sp.sys_prin
            ORDER BY ida.area
            FOR JSON PATH
          ) AS [invalidDelivAreas],

          /* vendorSentTo (I) */
          ( SELECT
              vst.sys_prin AS [sysPrin],
              vst.vend_id  AS [vendorId],
              vst.queformail_cd AS [queForMail],
              ( SELECT
                  v.vend_id  AS [id], v.vend_nm AS [name], v.vend_actv_cd AS [active],
                  v.vend_rcvr_cd AS [receiver], v.vend_fsrv_nm AS [fileServerName],
                  v.vend_fsrv_ip AS [fileServerIp], v.fsrvr_user_id AS [fileServerUserId],
                  v.fsrvr_usr_pwd_tx AS [fileServerPassword], v.fsrvr_file_name_tx AS [fileName],
                  v.fsrvr_unc_share_tx AS [uncShare], v.fsrvr_path_tx AS [path],
                  v.fsrvr_file_archive_path_tx AS [archivePath], v.vendor_type_cd AS [vendorTypeCode],
                  v.vend_file_io AS [fileIo], v.vend_client_specific AS [clientSpecific],
                  v.vend_schedule AS [schedule], v.vend_date_last_worked AS [dateLastWorked],
                  v.vend_filesize AS [fileSize], v.vend_filetrans_specs AS [fileTransferSpecs],
                  v.vend_file_type AS [fileType], v.ftp_passive AS [ftpPassive], v.ftp_filetype AS [ftpFileType]
                FOR JSON PATH, WITHOUT_ARRAY_WRAPPER
              ) AS [vendor]
            FROM vendor_sent_to vst
            JOIN vendor v ON v.vend_id = vst.vend_id AND v.vend_file_io = 'I'
            WHERE vst.sys_prin = sp.sys_prin
            ORDER BY vst.vend_id
            FOR JSON PATH
          ) AS [vendorSentTo],

          /* vendorReceivedFrom (O) */
          ( SELECT
              vrf.sys_prin AS [sysPrin],
              vrf.vend_id  AS [vendorId],
              vrf.queformail_cd AS [queForMail],
              ( SELECT
                  v.vend_id  AS [id], v.vend_nm AS [name], v.vend_actv_cd AS [active],
                  v.vend_rcvr_cd AS [receiver], v.vend_fsrv_nm AS [fileServerName],
                  v.vend_fsrv_ip AS [fileServerIp], v.fsrvr_user_id AS [fileServerUserId],
                  v.fsrvr_usr_pwd_tx AS [fileServerPassword], v.fsrvr_file_name_tx AS [fileName],
                  v.fsrvr_unc_share_tx AS [uncShare], v.fsrvr_path_tx AS [path],
                  v.fsrvr_file_archive_path_tx AS [archivePath], v.vendor_type_cd AS [vendorTypeCode],
                  v.vend_file_io AS [fileIo], v.vend_client_specific AS [clientSpecific],
                  v.vend_schedule AS [schedule], v.vend_date_last_worked AS [dateLastWorked],
                  v.vend_filesize AS [fileSize], v.vend_filetrans_specs AS [fileTransferSpecs],
                  v.vend_file_type AS [fileType], v.ftp_passive AS [ftpPassive], v.ftp_filetype AS [ftpFileType]
                FOR JSON PATH, WITHOUT_ARRAY_WRAPPER
              ) AS [vendor]
            FROM vendor_sent_to vrf
            JOIN vendor v ON v.vend_id = vrf.vend_id AND v.vend_file_io = 'O'
            WHERE vrf.sys_prin = sp.sys_prin
            ORDER BY vrf.vend_id
            FOR JSON PATH
          ) AS [vendorReceivedFrom]

        FROM sys_prins sp
        WHERE sp.client = :client
          AND sp.sys_prin = :sysPrin
        FOR JSON PATH
      ), N'[]') AS full_json;
