UPDATE t
SET    cust_type = '1'          -- CUST_TYPE is VARCHAR(1), so quote it
FROM   dbo.SYS_PRINS AS t
WHERE  t.Id = (
  SELECT MIN(Id)
  FROM dbo.SYS_PRINS
  WHERE CLIENT = '0034' AND SYS_PRIN = '90720700'
);
