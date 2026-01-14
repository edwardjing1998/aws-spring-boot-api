/* vendorSentToTotal (scalar; IO='I') */
JSON_QUERY((
    SELECT
      COUNT(1) AS [value]
    FROM vendor_sent_to vrf
    WHERE vrf.sys_prin = sp.sys_prin
      AND EXISTS (
        SELECT 1
        FROM VENDOR vv
        WHERE vv.vend_id = vrf.vend_id
          AND vv.vend_file_io = 'I'
      )
    FOR JSON PATH, WITHOUT_ARRAY_WRAPPER
)) AS [vendorSentToTotal],
