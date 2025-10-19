2025-10-19T11:10:17.724-05:00  INFO 16988 --- [client-sysprin-writer] [0.0-8085-exec-1] o.springdoc.api.AbstractOpenApiResource  : Init duration for springdoc-openapi is: 17546 ms
Hibernate: select sp1_0.client,sp1_0.sys_prin,sp1_0.active,sp1_0.addr_flag,sp1_0.a_stat_rch,sp1_0.bad_state,sp1_0.contact,sp1_0.cust_type,sp1_0.des_stat,sp1_0.entity_cd,sp1_0.forwarding_addr,sp1_0.hold_days,sp1_0.nm_13,sp1_0.non_us,sp1_0.notes,sp1_0.phone,sp1_0.pin,sp1_0.po_box,sp1_0.report_method,sp1_0.ret_stat,sp1_0.rps,sp1_0.session,sp1_0.special,sp1_0.stat_a,sp1_0.stat_b,sp1_0.stat_c,sp1_0.stat_d,sp1_0.stat_e,sp1_0.stat_f,sp1_0.stat_i,sp1_0.stat_l,sp1_0.stat_o,sp1_0.stat_u,sp1_0.stat_x,sp1_0.stat_z,sp1_0.temp_away,sp1_0.temp_away_atts,sp1_0.undeliverable from sys_prins sp1_0 where ((sp1_0.client=? and sp1_0.sys_prin=?))
2025-10-19T11:13:31.530-05:00  INFO 16988 --- [client-sysprin-writer] [0.0-8085-exec-3] o.h.e.internal.DefaultLoadEventListener  : HHH000327: Error performing load command

org.hibernate.HibernateException: Duplicate row was found and `ASSERT` was specified
        at org.hibernate.sql.results.spi.ListResultsConsumer.readUniqueAssert(ListResultsConsumer.java:264) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.sql.results.spi.ListResultsConsumer.consume(ListResultsConsumer.java:198) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.sql.results.spi.ListResultsConsumer.consume(ListResultsConsumer.java:35) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.sql.exec.internal.JdbcSelectExecutorStandardImpl.doExecuteQuery(JdbcSelectExecutorStandardImpl.java:224) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
