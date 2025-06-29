? and dat1_0.date_time=? and dat1_0.trans_no=?))
Hibernate: select dat1_0.case_number,dat1_0.date_time,dat1_0.trans_no,dat1_0.account,dat1_0.action_id,dat1_0.action_reason,dat1_0.document_no,dat1_0.no_cards,dat1_0.operator_time,dat1_0.pi_id,dat1_0.postage_category_cd,dat1_0.sys_prin,dat1_0.uid,dat1_0.workstation_name_tx from deleted_account_trans dat1_0 where ((dat1_0.case_number=? and dat1_0.date_time=? and dat1_0.trans_no=?))
Hibernate: select da1_0.address_entity_cd,da1_0.case_number_id,da1_0.addr1_tx,da1_0.addr2_tx,da1_0.addr3_tx,da1_0.addr4_tx,da1_0.alternate_name_tx,da1_0.city_tx,da1_0.delivery_point_cd,da1_0.state_tx,da1_0.zip_cd from deleted_addresses da1_0 where ((da1_0.address_entity_cd=? and da1_0.case_number_id=?))
Hibernate: select da1_0.address_entity_cd,da1_0.case_number_id,da1_0.addr1_tx,da1_0.addr2_tx,da1_0.addr3_tx,da1_0.addr4_tx,da1_0.alternate_name_tx,da1_0.city_tx,da1_0.delivery_point_cd,da1_0.state_tx,da1_0.zip_cd from deleted_addresses da1_0 where ((da1_0.address_entity_cd=? and da1_0.case_number_id=?))
Hibernate: select da1_0.address_entity_cd,da1_0.case_number_id,da1_0.addr1_tx,da1_0.addr2_tx,da1_0.addr3_tx,da1_0.addr4_tx,da1_0.alternate_name_tx,da1_0.city_tx,da1_0.delivery_point_cd,da1_0.state_tx,da1_0.zip_cd from deleted_addresses da1_0 where ((da1_0.address_entity_cd=? and da1_0.case_number_id=?))
Hibernate: select dt1_0.CASE_NUMBER,dt1_0.date_time,dt1_0.trans_no,dt1_0.case_number,dt1_0.command_line,dt1_0.cycle,dt1_0.retry_count,dt1_0.system_type,dt1_0.type from deleted_transactions dt1_0 where ((dt1_0.CASE_NUMBER=? and dt1_0.date_time=? and dt1_0.trans_no=?))
2025-06-28T22:01:48.854-05:00 ERROR 13524 --- [delete-case] [0.0-8082-exec-1] o.a.c.c.C.[.[.[/].[dispatcherServlet]    : Servlet.service() for servlet [dispatcherServlet] in context with path [] threw exception [Request processing failed: java.lang.RuntimeException: java.lang.StackOverflowError] with root cause

java.lang.StackOverflowError: null
        at rapid.model.cases.cases.DltCase.toString(DltCase.java:21) ~[common-model-0.0.1-SNAPSHOT.jar:na]
        at java.base/java.lang.String.valueOf(String.java:4543) ~[na:na]
        at rapid.model.cases.accountTransaction.DltAccountTransaction.toString(DltAccountTransaction.java:11) ~[common-model-0.0.1-SNAPSHOT.jar:na]
        at java.base/java.lang.String.valueOf(String.java:4543) ~[na:na]
        at java.base/java.lang.StringBuilder.append(StringBuilder.java:173) ~[na:na]
        at java.base/java.util.AbstractCollection.toString(AbstractCollection.java:459) ~[na:na]
        at java.base/java.lang.String.valueOf(String.java:4543) ~[na:na]
        at rapid.model.cases.cases.DltCase.toString(DltCase.java:21) ~[common-model-0.0.1-SNAPSHOT.jar:na]
        at java.base/java.lang.String.valueOf(String.java:4543) ~[na:na]
        at rapid.model.cases.accountTransaction.DltAccountTransaction.toString(DltAccountTransaction.java:11) ~[common-model-0.0.1-SNAPSHOT.jar:na]
        at java.base/java.lan
