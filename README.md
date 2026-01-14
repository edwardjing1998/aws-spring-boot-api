SELECT COUNT(*) AS vendorSentToTotal
FROM vendor_sent_to vrf
WHERE vrf.sys_prin = sp.sys_prin
  AND EXISTS (
      SELECT 1
      FROM VENDOR vv
      WHERE vv.vend_id = vrf.vend_id
        AND vv.vend_file_io = 'I'
  );
