/* vendorReceivedFrom count (TOP 10 only; only vendors with IO='O') */
(
  SELECT COUNT(*)
  FROM (
    SELECT TOP (10)
      vrf.sys_prin,
      vrf.vend_id,
      vrf.queformail_cd
    FROM vendor_sent_to vrf
    WHERE vrf.sys_prin = sp.sys_prin
      AND EXISTS (
        SELECT 1
        FROM VENDOR vv
        WHERE vv.vend_id = vrf.vend_id
          AND vv.vend_file_io = 'O'
      )
    ORDER BY vrf.vend_id
  ) x
) AS [vendorReceivedFromCount]
