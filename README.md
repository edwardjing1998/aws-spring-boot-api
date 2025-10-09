Hibernate: select ce1_0.client_id,ce1_0.email_address_tx,ce1_0.active_flag,ce1_0.carbon_copy_flag,ce1_0.email_name_tx,ce1_0.mail_server_id,ce1_0.report_id from client_email ce1_0 where ce1_0.client_id=?
2025-10-09T10:39:29.732-05:00  INFO 38372 --- [client-sysprin-writer] [0.0-8085-exec-1] rapid.service.client.ClientService       : Deleting client with Associated entities clientId: 0001. - ReportOptions: 0, SysPrinsPrefixes: 0, SysPrins: 4, ClientEmails: 4
Hibernate: delete from client_email where client_id=? and email_address_tx=?
Hibernate: delete from client_email where client_id=? and email_address_tx=?
Hibernate: delete from client_email where client_id=? and email_address_tx=?
Hibernate: delete from client_email where client_id=? and email_address_tx=?
Hibernate: delete from sys_prins where client=? and sys_prin=?
2025-10-09T10:39:31.292-05:00  INFO 38372 --- [client-sysprin-writer] [0.0-8085-exec-1] rapid.service.client.ClientService       : Error deleting client with ID: 0001. Error: Duplicate identifier in table (sys_prins) - rapid.model.sysprin.SysPrin#SysPrinId(client=0001, sysPrin=01010001)
