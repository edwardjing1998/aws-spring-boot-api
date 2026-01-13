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
  WHERE x.rn <= 10
) AS [vendorReceivedFromCount]
