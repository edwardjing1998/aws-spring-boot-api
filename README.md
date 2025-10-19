Hibernate: update sys_prins set active=?,addr_flag=?,a_stat_rch=?,bad_state=?,contact=?,cust_type=?,des_stat=?,entity_cd=?,forwarding_addr=?,hold_days=?,nm_13=?,non_us=?,notes=?,phone=?,pin=?,po_box=?,report_method=?,ret_stat=?,rps=?,session=?,special=?,stat_a=?,stat_b=?,stat_c=?,stat_d=?,stat_e=?,stat_f=?,stat_i=?,stat_l=?,stat_o=?,stat_u=?,stat_x=?,stat_z=?,temp_away=?,temp_away_atts=?,undeliverable=? where client=? and sys_prin=?
2025-10-19T11:34:11.727-05:00 ERROR 3736 --- [client-sysprin-writer] [0.0-8085-exec-1] o.a.c.c.C.[.[.[/].[dispatcherServlet]    : Servlet.service() for servlet [dispatcherServlet] in context with path [] threw exception [Request processing failed: org.springframework.orm.jpa.JpaSystemException: Duplicate identifier in table (sys_prins) - rapid.model.sysprin.SysPrin#SysPrinId(client=0016, sysPrin=57491000)] with root cause

org.hibernate.HibernateException: Duplicate identifier in table (sys_prins) - rapid.model.sysprin.SysPrin#SysPrinId(client=0016, sysPrin=57491000)
        at org.hibernate.engine.jdbc.mutation.internal.ModelMutationHelper.identifiedResultsCheck(ModelMutationHelper.java:81) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.persister.entity.mutation.UpdateCoordinatorStandard.lambda$doStaticUpdate$9(UpdateCoordinatorStandard.java:785) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.engine.jdbc.mutation.internal.ModelMutationHelper.checkResults(ModelMutationHelper.java:50) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.engine.jdbc.mutation.internal.AbstractMutationExecutor.performNonBatchedMutation(AbstractMutationExecutor.java:141) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.engine.jdbc.mutation.internal.MutationExecutorSingleNonBatched.performNonBatchedOperations(MutationExecutorSingleNonBatched.java:55) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.engine.jdbc.mutation.internal.AbstractMutationExecutor.execute(AbstractMutationExecutor.java:55) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.persister.entity.mutation.UpdateCoordinatorStandard.doStaticUpdate(U









        
