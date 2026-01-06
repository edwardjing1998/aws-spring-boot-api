select count(*) from vendor v where v.Vend_file_IO = 'O' and v.Vend_id not in (select vst.vend_id from Vendor_Sent_to vst where vst.sys_prin = '10757630');
