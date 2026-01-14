/* vendorReceivedFromTotal (TOTAL; IO='O') */
(
  SELECT COUNT_BIG(*)
  FROM vendor_sent_to vrf
  WHERE vrf.sys_prin = sp.sys_prin
    AND EXISTS (
      SELECT 1
      FROM vendor vv
      WHERE vv.vend_id = vrf.vend_id
        AND vv.vend_file_io = 'O'
    )
) AS [vendorReceivedFromTotal],
