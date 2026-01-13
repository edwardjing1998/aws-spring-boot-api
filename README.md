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
