SELECT COUNT_BIG(*)
FROM dbo.Vendor v
WHERE v.Vend_file_IO = 'O'
  AND NOT EXISTS (
      SELECT 1
      FROM dbo.Vendor_Sent_to vst
      WHERE vst.sys_prin = '10757630'
        AND vst.vend_id  = v.vend_id
  );
