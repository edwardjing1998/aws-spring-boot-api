// ---------- CREATE (mode === 'new') ----------
const handleCreate = async () => {
  try {
    const { client, sysPrin, body } = buildPayload();

    // POST http://localhost:8089/client-sysprin-writer/api/sysprins/create/{client}/{sysPrin}
    const url =
      `http://localhost:8089/client-sysprin-writer/api/sysprins/create/` +
      `${encodeURIComponent(client)}/${encodeURIComponent(sysPrin)}`;

    const res = await fetch(url, {
      method: 'POST',
      headers: {
        accept: '*/*',
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        // include these explicitly to mirror your curl body
        client,
        sysPrin,
        ...body,
      }),
    });

    if (!res.ok) {
      const text = await res.text().catch(() => '');
      throw new Error(`Create failed (HTTP ${res.status}). ${text}`);
    }

    const saved = await res.json().catch(() => null);
    alert('Created successfully.');
    if (saved && typeof saved === 'object') {
      setSelectedData(prev => ({ ...prev, ...saved }));
    }
  } catch (err) {
    console.error('Create error:', err);
    alert(`Create error: ${err.message ?? err}`);
  }
};





Hibernate: insert into sys_prins (active,addr_flag,a_stat_rch,bad_state,contact,cust_type,des_stat,entity_cd,forwarding_addr,hold_days,nm_13,non_us,notes,phone,pin,po_box,report_method,ret_stat,rps,session,special,stat_a,stat_b,stat_c,stat_d,stat_e,stat_f,stat_i,stat_l,stat_o,stat_u,stat_x,stat_z,temp_away,temp_away_atts,undeliverable,client,sys_prin) values (?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?)
2025-10-22T13:44:55.285-05:00  WARN 21816 --- [client-sysprin-writer] [0.0-8085-exec-8] o.h.engine.jdbc.spi.SqlExceptionHelper   : SQL Error: 515, SQLState: 23000
2025-10-22T13:44:55.379-05:00 ERROR 21816 --- [client-sysprin-writer] [0.0-8085-exec-8] o.h.engine.jdbc.spi.SqlExceptionHelper   : Cannot insert the value NULL into column 'ACTIVE', table 'Rapid3.dbo.SYS_PRINS'; column does not allow nulls. INSERT fails.
2025-10-22T13:44:56.185-05:00 ERROR 21816 --- [client-sysprin-writer] [0.0-8085-exec-8] o.a.c.c.C.[.[.[/].[dispatcherServlet]    : Servlet.service() for servlet [dispatcherServlet] in context with path [] threw exception [Request processing failed: org.springframework.dao.DataIntegrityViolationException: could not execute statement [Cannot insert the value NULL into column 'ACTIVE', table 'Rapid3.dbo.SYS_PRINS'; column does not allow nulls. INSERT fails.] [insert into sys_prins (active,addr_flag,a_stat_rch,bad_state,contact,cust_type,des_stat,entity_cd,forwarding_addr,hold_days,nm_13,non_us,notes,phone,pin,po_box,report_method,ret_stat,rps,session,special,stat_a,stat_b,stat_c,stat_d,stat_e,stat_f,stat_i,stat_l,stat_o,stat_u,stat_x,stat_z,temp_away,temp_away_atts,undeliverable,client,sys_prin) values (?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?)]; SQL [insert into sys_prins (active,addr_flag,a_stat_rch,bad_state,contact,cust_type,des_stat,entity_cd,forwarding_addr,hold_days,nm_13,non_us,notes,phone,pin,po_box,report_method,ret_stat,rps,session,special,stat_a,stat_b,stat_c,stat_d,stat_e,stat_f,stat_i,stat_l,stat_o,stat_u,stat_x,stat_z,temp_away,temp_away_atts,undeliverable,client,sys_prin) values (?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?)]; constraint [null]] with root cause

com.microsoft.sqlserver.jdbc.SQLServerException: Cannot insert the value NULL into column 'ACTIVE', table 'Rapid3.dbo.SYS_PRINS'; column does not allow nulls. INSERT fails.
        at com.microsoft.sqlserver.jdbc.SQLServerException.makeFromDatabaseError(SQLServerException.java:276) ~[mssql-jdbc-12.10.0.jre11.jar:na]
        at com.microsoft.sqlserver.jdbc.SQLServerStatement.getNextResult(SQLServerStatement.java:1787) ~[mssql-jdbc-12.10.0.jre11.jar:na]
        at com.microsoft.sqlserver.jdbc.SQLServerPreparedStatement.doExecutePreparedStatement(SQLServerPreparedStatement.java:688) ~[mssql-jdbc-12.10.0.jre11.jar:na]
        at com.microsoft.sqlserver.jdbc.SQLServerPreparedStatement$PrepStmtExecCmd.doExecute(SQLServerPreparedStatement.java:607) ~[mssql-jdbc-12.10.0.jre11.jar:na]
        at com.microsoft.sqlserver.jdbc.TDSCommand.execute(IOBuffer.java:7745) ~[mssql-jdbc-12.10.0.jre11.jar:na]
        at com.microsoft.sqlserver.jdbc.SQLServerConnection.executeCommand(SQLServerConnection.java:4700) ~[mssql-jdbc-12.10.0.jre11.jar:na]
        at com.microsoft.sqlserver.jdbc.SQLServerStatement.executeCommand(SQLServerStatement.java:321) ~[mssql-jdbc-12.10.0.jre11.jar:na]
        at com.microsoft.sqlserver.jdbc.SQLServerStatement.executeStatement(SQLServerStatement.java:253) ~[mssql-jdbc-12.10.0.jre11.jar:na]
        at com.microsoft.sqlserver.jdbc.SQLServerPreparedStatement.executeUpdate(SQLServerPreparedStatement.java:549) ~[mssql-jdbc-12.10.0.jre11.jar:na]
        at com.zaxxer.hikari.pool.ProxyPreparedStatement.executeUpdate(ProxyPreparedStatement.java:61) ~[HikariCP-6.3.0.jar:na]
        at com.zaxxer.hikari.pool.HikariProxyPreparedStatement.executeUpdate(HikariProxyPreparedStatement.java) ~[HikariCP-6.3.0.jar:na]
        at org.hibernate.engine.jdbc.internal.ResultSetReturnImpl.executeUpdate(ResultSetReturnImpl.java:194) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.engine.jdbc.mutation.internal.AbstractMutationExecutor.performNonBatchedMutation(AbstractMutationExecutor.java:134) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.engine.jdbc.mutation.internal.MutationExecutorSingleNonBatched.performNonBatchedOperations(MutationExecutorSingleNonBatched.java:55) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.engine.jdbc.mutation.internal.AbstractMutationExecutor.execute(AbstractMutationExecutor.java:55) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.persister.entity.mutation.InsertCoordinatorStandard.doStaticInserts(InsertCoordinatorStandard.java:194) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.persi


active: Boolean(data?.active ?? false), 
