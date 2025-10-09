public interface SysPrinRepository extends JpaRepository<SysPrin, SysPrinId> {
  @Modifying(clearAutomatically = true, flushAutomatically = true)
  @Query("delete from SysPrin sp where sp.id.client = :clientId")
  int hardDeleteByClient(@Param("clientId") String clientId);
}

public interface ClientEmailRepository extends JpaRepository<ClientEmail, ClientEmailId> {
  @Modifying(clearAutomatically = true, flushAutomatically = true)
  @Query("delete from ClientEmail ce where ce.id.clientId = :clientId")
  int hardDeleteByClient(@Param("clientId") String clientId);
}

public interface ClientReportOptionRepository extends JpaRepository<ClientReportOption, ClientReportOptionId> {
  @Modifying(clearAutomatically = true, flushAutomatically = true)
  @Query("delete from ClientReportOption cro where cro.id.clientId = :clientId")
  int hardDeleteByClient(@Param("clientId") String clientId);
}

// NOTE: your mapping ties prefixes to billingSp, not clientId.
public interface SysPrinsPrefixRepository extends JpaRepository<SysPrinsPrefix, SysPrinsPrefixId> {
  @Modifying(clearAutomatically = true, flushAutomatically = true)
  @Query("delete from SysPrinsPrefix spp where spp.id.billingSp = :billingSp")
  int hardDeleteByBillingSp(@Param("billingSp") String billingSp);
}




2025-10-09T12:08:52.523-05:00  INFO 36892 --- [client-sysprin-writer] [0.0-8085-exec-1] o.s.web.servlet.DispatcherServlet        : Completed initialization in 4 ms
2025-10-09T12:08:52.975-05:00  INFO 36892 --- [client-sysprin-writer] [0.0-8085-exec-1] r.client.web.ClientWriterController      : Deleting client details with ID: 0001
Hibernate: select c1_0.client,c1_0.active,c1_0.addr,c1_0.amex_issued,c1_0.billing_sp,c1_0.chlookup_type,c1_0.city,c1_0.contact,c1_0.exclude_from_report,c1_0.fax_number,c1_0.name,c1_0.phone,c1_0.positive_reports,c1_0.report_break_flag,c1_0.state,c1_0.sub_client_ind,c1_0.sub_client_xref,c1_0.zip from clients c1_0 where c1_0.client=?
2025-10-09T12:08:53.840-05:00  INFO 36892 --- [client-sysprin-writer] [0.0-8085-exec-1] o.h.e.internal.DefaultLoadEventListener  : HHH000327: Error performing load command

org.hibernate.HibernateException: Duplicate row was found and `ASSERT` was specified
        at org.hibernate.sql.results.spi.ListResultsConsumer.readUniqueAssert(ListResultsConsumer.java:264) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.sql.results.spi.ListResultsConsumer.consume(ListResultsConsumer.java:198) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.sql.results.spi.ListResultsConsumer.consume(ListResultsConsumer.java:35) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.sql.exec.internal.JdbcSelectExecutorStandardImpl.doExecuteQuery(JdbcSelectExecutorStandardImpl.java:224) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.sql.exec.internal.JdbcSelectExecutorStandardImpl.executeQuery(JdbcSelectExecutorStandardImpl.java:102) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.sql.exec.spi.JdbcSelectExecutor.executeQuery(JdbcSelectExecutor.java:91) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.sql.exec.spi.JdbcSelectExecutor.list(JdbcSelectExecutor.java:165) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.loader.ast.internal.SingleIdLoadPlan.load(SingleIdLoadPlan.java:145) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.loader.ast.internal.SingleIdLoadPlan.load(SingleIdLoadPlan.java:117) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.loader.ast.internal.SingleIdEntityLoaderStandardImpl.load(SingleIdEntityLoaderStandardImpl.java:74) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.persister.entity.AbstractEntityPersister.doLoad(AbstractEntityPersister.java:3895) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.persister.entity.AbstractEntityPersister.load(AbstractEntityPersister.java:3884) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.event.internal.DefaultLoadEventListener.loadFromDatasource(DefaultLoadEventListener.java:604) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.event.internal.DefaultLoadEventListener.loadFromCacheOrDatasource(DefaultLoadEventListener.java:590) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.event.internal.DefaultLoadEventListener.load(DefaultLoadEventListener.java:560) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.event.internal.DefaultLoadEventListener.doLoad(DefaultLoadEventListener.java:544) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.event.internal.DefaultLoadEventListener.load(DefaultLoadEventListener.java:206) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.event.internal.DefaultLoadEventListener.loadWithRegularProxy(DefaultLoadEventListener.java:289) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.event.internal.DefaultLoadEventListener.proxyOrLoad(DefaultLoadEventListener.java:241) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.event.internal.DefaultLoadEventListener.doOnLoad(DefaultLoadEventListener.java:110) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.event.internal.DefaultLoadEventListener.onLoad(DefaultLoadEventListener.java:69) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.event.service.internal.EventListenerGroupImpl.fireEventOnEachListener(EventListenerGroupImpl.java:138) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.internal.SessionImpl.fireLoadNoChecks(SessionImpl.java:1229) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.internal.SessionImpl.fireLoad(SessionImpl.java:1217) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.loader.internal.IdentifierLoadAccessImpl.load(IdentifierLoadAccessImpl.java:210) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.loader.internal.IdentifierLoadAccessImpl.doLoad(IdentifierLoadAccessImpl.java:161) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.loader.internal.IdentifierLoadAccessImpl.lambda$load$1(IdentifierLoadAccessImpl.java:150) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.loader.internal.IdentifierLoadAccessImpl.perform(IdentifierLoadAccessImpl.java:113) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.loader.internal.IdentifierLoadAccessImpl.load(IdentifierLoadAccessImpl.java:150) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.internal.SessionImpl.find(SessionImpl.java:2459) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.internal.SessionImpl.find(SessionImpl.java:2430) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at java.base/jdk.internal.reflect.DirectMethodHandleAccessor.invoke(DirectMethodHandleAccessor.java:104) ~[na:na]
        at java.base/java.lang.reflect.Method.invoke(Method.java:565) ~[na:na]
        at org.springframework.orm.jpa.ExtendedEntityManagerCreator$ExtendedEntityManagerInvocationHandler.invoke(ExtendedEntityManagerCreator.java:364) ~[spring-orm-6.2.7.jar:6.2.7]
        at jdk.proxy2/jdk.proxy2.$Proxy156.find(Unknown Source) ~[na:na]
        at java.base/jdk.internal.reflect.DirectMethodHandleAccessor.invoke(DirectMethodHandleAccessor.java:104) ~[na:na]
        at java.base/java.lang.reflect.Method.invoke(Method.java:565) ~[na:na]
        at org.springframework.orm.jpa.SharedEntityManagerCreator$SharedEntityManagerInvocationHandler.invoke(SharedEntityManagerCreator.java:320) ~[spring-orm-6.2.7.jar:6.2.7]
        at jdk.proxy2/jdk.proxy2.$Proxy156.find(Unknown Source) ~[na:na]
        at org.springframework.data.jpa.repository.support.SimpleJpaRepository.findById(SimpleJpaRepository.java:332) ~[spring-data-jpa-3.5.0.jar:3.5.0]
        at java.base/jdk.internal.reflect.DirectMethodHandleAccessor.invoke(DirectMethodHandleAccessor.java:104) ~[na:na]
        at java.base/java.lang.reflect.Method.invoke(Method.java:565) ~[na:na]
        at org.springframework.aop.support.AopUtils.invokeJoinpointUsingReflection(AopUtils.java:359) ~[spring-aop-6.2.7.jar:6.2.7]
        at org.springframework.data.repository.core.support.RepositoryMethodInvoker$RepositoryFragmentMethodInvoker.lambda$new$0(RepositoryMethodInvoker.java:277) ~[spring-data-commons-3.5.0.jar:3.5.0]
        at org.springframework.data.repository.core.support.RepositoryMethodInvoker.doInvoke(RepositoryMethodInvoker.java:170) ~[spring-data-commons-3.5.0.jar:3.5.0]
        at org.springframework.data.repository.core.support.RepositoryMethodInvoker.invoke(RepositoryMethodInvoker.java:158) ~[spring-data-commons-3.5.0.jar:3.5.0]
        at org.springframework.data.repository.core.support.RepositoryComposition$RepositoryFragments.invoke(RepositoryComposition.java:515) ~[spring-data-commons-3.5.0.jar:3.5.0]
        at org.springframework.data.repository.core.support.RepositoryComposition.invoke(RepositoryComposition.java:284) ~[spring-data-commons-3.5.0.jar:3.5.0]
        at org.springframework.data.repository.core.support.RepositoryFactorySupport$ImplementationMethodExecutionInterceptor.invoke(RepositoryFactorySupport.java:734) ~[spring-data-commons-3.
















@Service
@RequiredArgsConstructor
public class ClientService {
  private final ClientRepository clientRepo;
  private final SysPrinRepository sysPrinRepo;
  private final ClientEmailRepository emailRepo;
  private final ClientReportOptionRepository reportRepo;
  private final SysPrinsPrefixRepository prefixRepo;

  @Transactional
  public void deleteClientDeep(String clientId) {
    Client client = clientRepo.findById(clientId)
        .orElseThrow(() -> new IllegalArgumentException("Client not found: " + clientId));

    // 1) bulk-delete children (no hydration => no duplicate-id errors)
    emailRepo.hardDeleteByClient(clientId);
    reportRepo.hardDeleteByClient(clientId);
    sysPrinRepo.hardDeleteByClient(clientId);
    if (client.getBillingSp() != null) {
      prefixRepo.hardDeleteByBillingSp(client.getBillingSp());
    }

    // 2) delete the parent
    clientRepo.deleteById(clientId);
  }
}





  @Modifying(clearAutomatically = true, flushAutomatically = true)
  @Query("delete from Client c where c.client = :clientId")
  int hardDeleteByClient(@Param("clientId") String clientId);







