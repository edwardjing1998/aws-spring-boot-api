COLUMN_NAME	DATA_TYPE	IS_NULLABLE	CHARACTER_MAXIMUM_LENGTH
case_number_id	char	YES	12
address_entity_cd	char	NO	1
alternate_name_tx	char	YES	50
addr1_tx	char	YES	50
addr2_tx	char	YES	50
city_tx	char	YES	25
state_tx	char	YES	2
zip_cd	char	YES	10
Delivery_Point_CD	char	YES	2
addr3_tx	char	YES	50
addr4_tx	char	YES	50






SELECT AT.CASE_NUMBER, AT.ACTION_REASON,
       AT.DATE_TIME, AT.pi_id, AT.ACCOUNT, C.last_name, C.first_name,
       A.addr1_tx, A.addr2_tx, A.city_tx, A.state_tx, A.zip_cd,
       C.SYS_PRIN, CL.client, CL.name
FROM   ACCOUNT_TRANS AT, CASES C, CLIENTS CL, addresses A, SYS_PRINS SP 
WHERE  AT.ACTION_ID = 'ROA'
  AND  C.CASE_NUMBER = AT.CASE_NUMBER 
  AND  (C.CASE_NUMBER = A.CASE_NUMBER_ID AND A.address_entity_cd = '1')
  AND  AT.sys_prin = SP.sys_prin 
  AND  CL.Client = SP.Client


  SELECT AT.CASE_NUMBER, AT.ACTION_REASON,
       AT.DATE_TIME, AT.pi_id, AT.ACCOUNT, C.last_name, C.first_name,
       A.addr1_tx, A.addr2_tx, A.city_tx, A.state_tx, A.zip_cd,
       C.SYS_PRIN, CL.client, CL.name
FROM   ACCOUNT_TRANS AT, CASES C, CLIENTS CL, addresses A, SYS_PRINS SP 
WHERE  AT.ACTION_ID = 'ROA'
  AND  AT.date_time >= 'dFromDate'
  AND  AT.date_time < 'dToDate + 1'
  AND  C.CASE_NUMBER = AT.CASE_NUMBER 
  AND  (C.CASE_NUMBER = A.CASE_NUMBER_ID AND A.address_entity_cd = '1')
  AND  AT.sys_prin = SP.sys_prin 
  AND  CL.Client = SP.Client
  [AND SP.client = 'sClient']  



PI_ID	char	YES	16
account	char	YES	16
case_number	char	NO	12
action_id	char	YES	3
uid	char	YES	10
date_time	datetime	YES	NULL
document_no	char	YES	25
sys_prin	char	YES	12
trans_no	numeric	NO	NULL
no_cards	int	YES	NULL
action_reason	varchar	YES	255
Operator_Time	int	YES	NULL
WorkStation_name_tx	char	YES	18
Postage_Category_cd	smallint	YES	NULL
Alt_Acct_ID	char	YES	19
Member_Seq_ID	char	YES	5

ACCOUNT_TRANS



Case_number	char	YES	12
pi_id	char	YES	16
Account	char	YES	16
DATE_TIME	datetime	YES	NULL
Non_Mon	int	YES	NULL
Retry_count	int	YES	NULL
Message	varchar	YES	255

FAILED_TRANS_WORK


case_number	char	YES	12
type	smallint	YES	NULL
command_line	varchar	YES	255
system_type	varchar	YES	50
retry_count	smallint	YES	NULL
date_time	datetime	YES	NULL
cycle	varchar	YES	1
trans_no	numeric	YES	NULL

FAILED_TRANS


SELECT 
    ft.CASE_NUMBER, 
    at.PI_ID, 
    ft.date_time, 
    ft.retry_count, 
    ft.command_line
FROM 
    FAILED_TRANS ft
JOIN
    ACCOUNT_TRANS at ON ft.trans_no = at.trans_no
WHERE
    ft.type = 1

    EXECUTE FRPC 'CUSTOMER_UPDATE', '', @ADDR_CNTN_LINE_ONE_TX='6855 PACIFIC STREET', @ADDR_CNTN_LINE_TWO_TX='', @CITY_NM='OMAHA', @ADDR_SBDV_ONE_TX='NE', @PSTL_CD='68106'
EXECUTE FRPC 'CONTACT_HISTORY_ADD', '5480302000296846', 'RAPID', '', '', 'RRD PI:5480303000546412 1 card(s) placed in research.  '
EXECUTE FRPC 'CUSTOMER_UPDATE', 'C12360110239771782028053', @ADDR_CNTN_LINE_ONE_TX='2000 N LAUDERDALE AVE APT 106', @ADDR_CNTN_LINE_TWO_TX='', @CITY_NM='TAMPA', @ADDR_SBDV_ONE_TX='FL', @PSTL_CD='33608-4771'
EXEC FRPC 'CNONMON','4470431112096227','698','00',,'BLL1','F',,,,,,,'4543 KNOLLCROFT ROAD',' ',,,'TROTWOOD','OH','USA','45426-1936',,,,,'P',,,,,'U'






  

