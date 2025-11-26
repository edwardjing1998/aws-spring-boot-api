package rapid.model.sysprin;

import jakarta.persistence.*;
import lombok.Data;
import rapid.model.sysprin.base.BaseSysPrin;
import rapid.model.sysprin.vendor.VendorSentTo;

import java.util.ArrayList;
import java.util.List;

@Entity
@Table(name = "SYS_PRINS")
@Data
public class SysPrin  extends BaseSysPrin {

    @OneToMany(mappedBy = "id.sysPrin", cascade = CascadeType.ALL, orphanRemoval = true)
    private List<VendorSentTo> vendorSentTo = new ArrayList<>();
}



package rapid.model.sysprin.vendor;

import jakarta.persistence.*;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import rapid.model.client.Client;
import rapid.model.sysprin.SysPrin;
import rapid.model.sysprin.base.BaseVendorSentTo;
import rapid.model.sysprin.key.VendorSentToId;

@Entity
@Table(name = "vendor_sent_to")
@NoArgsConstructor
@Data
public class VendorSentTo extends BaseVendorSentTo {
    public VendorSentTo(VendorSentToId id, Boolean queForMail) {
        super(id, queForMail);
    }

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "VEND_ID", referencedColumnName = "VEND_ID", insertable = false, updatable = false)
    private Vendor vendor;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(
            name = "SYS_PRIN",
            referencedColumnName = "SYS_PRIN",
            insertable = false,
            updatable = false
    )
    private SysPrin sysPrin;
}



mvn clean install                          
WARNING: A restricted method in java.lang.System has been called
WARNING: java.lang.System::load has been called by org.fusesource.jansi.internal.JansiLoader in an unnamed module (file:/C:/Program%20Files/Maven/apache-maven-3.9.6/lib/jansi-2.4.0.jar)
WARNING: Use --enable-native-access=ALL-UNNAMED to avoid a warning for callers in this module
WARNING: Restricted methods will be blocked in a future release unless native access is enabled

WARNING: A terminally deprecated method in sun.misc.Unsafe has been called
WARNING: sun.misc.Unsafe::objectFieldOffset has been called by com.google.common.util.concurrent.AbstractFuture$UnsafeAtomicHelper (file:/C:/Program%20Files/Maven/apache-maven-3.9.6/lib/guava-32.0.1-jre.jar)
WARNING: Please consider reporting this to the maintainers of class com.google.common.util.concurrent.AbstractFuture$UnsafeAtomicHelper
WARNING: sun.misc.Unsafe::objectFieldOffset will be removed in a future release
[INFO] Scanning for projects...
[INFO] ------------------------------------------------------------------------
[INFO] Reactor Build Order:
[INFO] 
[INFO] trace-client-sysprin-service                                       [pom]
[INFO] common-model                                                       [jar]
[INFO] common-api-dto                                                     [jar]
[INFO] common-mapper                                                      [jar]
[INFO] common-services                                                    [jar]
[INFO] client-sysprin-writer                                              [jar]
[INFO] client-sysprin-reader                                              [jar]
[INFO] gateway                                                            [jar]
[INFO] search-integration                                                 [jar]
[INFO] 
[INFO] --------< trace-client-sysprin-service:client-sysprin-service >---------
[INFO] Building trace-client-sysprin-service 0.0.1-SNAPSHOT               [1/9]
[INFO]   from pom.xml
[INFO] --------------------------------[ pom ]---------------------------------
[INFO] 
[INFO] --- clean:3.2.0:clean (default-clean) @ client-sysprin-service ---
[INFO] 
[INFO] --- install:3.1.1:install (default-install) @ client-sysprin-service ---
[INFO] Installing C:\Users\F2LIPBX\spring_boot\harish\trace-clients\pom.xml to C:\Users\F2LIPBX\.m2\repository\trace-client-sysprin-service\client-sysprin-service\0.0.1-SNAPSHOT\client-sysprin-service-0.0.1-SNAPSHOT.pom
[INFO] 
[INFO] -------------< trace-client-sysprin-service:common-model >--------------
[INFO] Building common-model 0.0.1-SNAPSHOT                               [2/9]
[INFO]   from common-model\pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- clean:3.2.0:clean (default-clean) @ common-model ---
[INFO] Deleting C:\Users\F2LIPBX\spring_boot\harish\trace-clients\common-model\target
[INFO] 
[INFO] --- resources:3.3.1:resources (default-resources) @ common-model ---
[WARNING] Using platform encoding (UTF-8 actually) to copy filtered resources, i.e. build is platform dependent!
[INFO] Copying 13 resources from src\main\resources to target\classes
[INFO] 
[INFO] --- compiler:3.13.0:compile (default-compile) @ common-model ---
[INFO] Recompiling the module because of changed source code.
[WARNING] File encoding has not been set, using platform encoding UTF-8, i.e. build is platform dependent!
[INFO] Compiling 44 source files with javac [debug parameters release 21] to target\classes
[INFO] /C:/Users/F2LIPBX/spring_boot/harish/trace-clients/common-model/src/main/java/rapid/model/client/ClientReportOption.java: C:\Users\F2LIPBX\spring_boot\harish\trace-clients\common-model\src\main\java\rapid\model\client\ClientReportOption.java uses or overrides a deprecated API.
[INFO] /C:/Users/F2LIPBX/spring_boot/harish/trace-clients/common-model/src/main/java/rapid/model/client/ClientReportOption.java: Recompile with -Xlint:deprecation for details.
[INFO] 
[INFO] --- resources:3.3.1:testResources (default-testResources) @ common-model ---
[WARNING] Using platform encoding (UTF-8 actually) to copy filtered resources, i.e. build is platform dependent!
[INFO] Copying 1 resource from src\test\resources to target\test-classes
[INFO] 
[INFO] --- compiler:3.13.0:testCompile (default-testCompile) @ common-model ---
[INFO] Recompiling the module because of changed dependency.
[WARNING] File encoding has not been set, using platform encoding UTF-8, i.e. build is platform dependent!
[INFO] Compiling 3 source files with javac [debug parameters release 21] to target\test-classes
[INFO] 
[INFO] --- surefire:3.2.2:test (default-test) @ common-model ---
[INFO] Using auto detected provider org.apache.maven.surefire.junitplatform.JUnitPlatformProvider
[INFO] 
[INFO] -------------------------------------------------------
[INFO]  T E S T S
[INFO] -------------------------------------------------------
[INFO] Running rapid.config.SwaggerConfigTest

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/

 :: Spring Boot ::                (v3.5.0)

2025-11-26T11:55:08.203-06:00  INFO 23656 --- [           main] rapid.config.SwaggerConfigTest           : Starting SwaggerConfigTest using Java 25.0.1 with PID 23656 (started by F2LIPBX in C:\Users\F2LIPBX\spring_boot\harish\trace-clients\common-model)
2025-11-26T11:55:08.209-06:00  INFO 23656 --- [           main] rapid.config.SwaggerConfigTest           : No active profile set, falling back to 1 default profile: "default"
2025-11-26T11:55:08.592-06:00  INFO 23656 --- [           main] rapid.config.SwaggerConfig               : Swagger Server URL is http://localhost:8089/test-service
2025-11-26T11:55:08.687-06:00  INFO 23656 --- [           main] rapid.config.SwaggerConfigTest           : Started SwaggerConfigTest in 1.163 seconds (process running for 2.934)
Mockito is currently self-attaching to enable the inline-mock-maker. This will no longer work in future releases of the JDK. Please add Mockito as an agent to your build as described in Mockito's documentation: https://javadoc.io/doc/org.mockito/mockito-core/latest/org.mockito/org/mockito/Mockito.html#0.3
WARNING: A Java agent has been loaded dynamically (C:\Users\F2LIPBX\.m2\repository\net\bytebuddy\byte-buddy-agent\1.17.5\byte-buddy-agent-1.17.5.jar)
WARNING: If a serviceability tool is in use, please run with -XX:+EnableDynamicAgentLoading to hide this warning
WARNING: If a serviceability tool is not in use, please run with -Djdk.instrument.traceUsage for more information
WARNING: Dynamic loading of agents will be disallowed by default in a future release
Java HotSpot(TM) 64-Bit Server VM warning: Sharing is only supported for boot loader classes because bootstrap classpath has been appended
[INFO] Tests run: 2, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 3.380 s -- in rapid.config.SwaggerConfigTest
[INFO] Running rapid.model.LiquibaseValidationTest
2025-11-26T11:55:11.106-06:00  INFO 23656 --- [           main] .s.d.r.c.RepositoryConfigurationDelegate : Bootstrapping Spring Data JPA repositories in DEFAULT mode.
2025-11-26T11:55:11.147-06:00  INFO 23656 --- [           main] .s.d.r.c.RepositoryConfigurationDelegate : Finished Spring Data repository scanning in 27 ms. Found 0 JPA repository interfaces.
2025-11-26T11:55:11.689-06:00  INFO 23656 --- [           main] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Starting...
2025-11-26T11:55:12.131-06:00  INFO 23656 --- [           main] com.zaxxer.hikari.pool.HikariPool        : HikariPool-1 - Added connection conn0: url=jdbc:h2:mem:testdb user=SA
2025-11-26T11:55:12.134-06:00  INFO 23656 --- [           main] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Start completed.
2025-11-26T11:55:12.254-06:00  INFO 23656 --- [           main] liquibase.database                       : Set default schema name to PUBLIC
2025-11-26T11:55:13.053-06:00  INFO 23656 --- [           main] liquibase.changelog                      : Creating database history table with name: PUBLIC.DATABASECHANGELOG
2025-11-26T11:55:13.232-06:00  INFO 23656 --- [           main] liquibase.changelog                      : Reading from PUBLIC.DATABASECHANGELOG
2025-11-26T11:55:13.477-06:00  INFO 23656 --- [           main] liquibase.snapshot                       : Creating snapshot
2025-11-26T11:55:13.735-06:00  INFO 23656 --- [           main] liquibase.lockservice                    : Successfully acquired change log lock
2025-11-26T11:55:13.737-06:00  INFO 23656 --- [           main] liquibase.command                        : Using deploymentId: 4179711651
2025-11-26T11:55:13.740-06:00  INFO 23656 --- [           main] liquibase.changelog                      : Reading from PUBLIC.DATABASECHANGELOG
2025-11-26T11:55:13.861-06:00  INFO 23656 --- [           main] liquibase.ui                             : Running Changeset: db/changelog/sysprin/create-vendor-sent-to.xml::create-vendor-sent-to::Edward Jing
2025-11-26T11:55:13.874-06:00  INFO 23656 --- [           main] liquibase.changelog                      : Table VENDOR_SENT_TO created
2025-11-26T11:55:13.883-06:00  INFO 23656 --- [           main] liquibase.changelog                      : Primary key added to VENDOR_SENT_TO (SYS_PRIN, VEND_ID)
2025-11-26T11:55:13.887-06:00  INFO 23656 --- [           main] liquibase.changelog                      : Index IDX_VENDOR_SENT_TO_SYSPRIN created
2025-11-26T11:55:13.888-06:00  INFO 23656 --- [           main] liquibase.changelog                      : ChangeSet db/changelog/sysprin/create-vendor-sent-to.xml::create-vendor-sent-to::Edward Jing ran successfully in 23ms
2025-11-26T11:55:13.902-06:00  INFO 23656 --- [           main] liquibase.ui                             : Running Changeset: db/changelog/sysprin/create-sys-prins-prefix-table.xml::create-sys-prins-prefix-table::Edward Jing
2025-11-26T11:55:13.907-06:00  INFO 23656 --- [           main] liquibase.changelog                      : Table SYS_PRINS_PREFIX created
2025-11-26T11:55:13.910-06:00  INFO 23656 --- [           main] liquibase.changelog                      : Primary key added to SYS_PRINS_PREFIX (BILLING_SP, PREFIX, ATM_CASH_RULE)
2025-11-26T11:55:13.911-06:00  INFO 23656 --- [           main] liquibase.changelog                      : ChangeSet db/changelog/sysprin/create-sys-prins-prefix-table.xml::create-sys-prins-prefix-table::Edward Jing ran successfully in 8ms
2025-11-26T11:55:13.917-06:00  INFO 23656 --- [           main] liquibase.ui                             : Running Changeset: db/changelog/sysprin/create-sys-prins-table.xml::create-sys-prins-table::Edward Jing
2025-11-26T11:55:13.922-06:00  INFO 23656 --- [           main] liquibase.changelog                      : Table SYS_PRINS created
2025-11-26T11:55:13.923-06:00  INFO 23656 --- [           main] liquibase.changelog                      : ChangeSet db/changelog/sysprin/create-sys-prins-table.xml::create-sys-prins-table::Edward Jing ran successfully in 6ms
2025-11-26T11:55:13.929-06:00  INFO 23656 --- [           main] liquibase.ui                             : Running Changeset: db/changelog/sysprin/create-vendor-table.xml::20240529-01-create-vendors::you
2025-11-26T11:55:13.933-06:00  INFO 23656 --- [           main] liquibase.changelog                      : Table VENDOR created
2025-11-26T11:55:13.934-06:00  INFO 23656 --- [           main] liquibase.changelog                      : ChangeSet db/changelog/sysprin/create-vendor-table.xml::20240529-01-create-vendors::you ran successfully in 5ms
2025-11-26T11:55:13.937-06:00  INFO 23656 --- [           main] liquibase.ui                             : Running Changeset: db/changelog/sysprin/create-invalid-delivery-areas-table.xml::create-invalid-delivery-areas-table::Edward Jing
2025-11-26T11:55:13.939-06:00  INFO 23656 --- [           main] liquibase.changelog                      : Table INVALID_DELIV_AREAS created
2025-11-26T11:55:13.941-06:00  INFO 23656 --- [           main] liquibase.changelog                      : Primary key added to INVALID_DELIV_AREAS (SYS_PRIN,AREA)
2025-11-26T11:55:13.942-06:00  INFO 23656 --- [           main] liquibase.changelog                      : ChangeSet db/changelog/sysprin/create-invalid-delivery-areas-table.xml::create-invalid-delivery-areas-table::Edward Jing ran successfully in 5ms
2025-11-26T11:55:13.948-06:00  INFO 23656 --- [           main] liquibase.ui                             : Running Changeset: db/changelog/client/create-clients-table.xml::create-clients-table::Edward Jing
2025-11-26T11:55:13.955-06:00  INFO 23656 --- [           main] liquibase.changelog                      : Table CLIENTS created
2025-11-26T11:55:13.956-06:00  INFO 23656 --- [           main] liquibase.changelog                      : ChangeSet db/changelog/client/create-clients-table.xml::create-clients-table::Edward Jing ran successfully in 6ms
2025-11-26T11:55:13.964-06:00  INFO 23656 --- [           main] liquibase.ui                             : Running Changeset: db/changelog/client/create-c3-transfer-parameters.xml::create-c3-transfer-parameters::harish c baswapuram
2025-11-26T11:55:13.972-06:00  INFO 23656 --- [           main] liquibase.changelog                      : Table C3_TRANSFER_PARAMETERS created
2025-11-26T11:55:13.972-06:00  INFO 23656 --- [           main] liquibase.changelog                      : ChangeSet db/changelog/client/create-c3-transfer-parameters.xml::create-c3-transfer-parameters::harish c baswapuram ran successfully in 7ms
2025-11-26T11:55:13.988-06:00  INFO 23656 --- [           main] liquibase.ui                             : Running Changeset: db/changelog/client/create-client-report-options-table.xml::create-client-report-options::Edward Jing
2025-11-26T11:55:13.996-06:00  INFO 23656 --- [           main] liquibase.changelog                      : Table CLIENT_REPORT_OPTIONS created
2025-11-26T11:55:13.998-06:00  INFO 23656 --- [           main] liquibase.changelog                      : Primary key added to CLIENT_REPORT_OPTIONS (CLIENT_ID, REPORT_ID)
2025-11-26T11:55:13.999-06:00  INFO 23656 --- [           main] liquibase.changelog                      : ChangeSet db/changelog/client/create-client-report-options-table.xml::create-client-report-options::Edward Jing ran successfully in 11ms
2025-11-26T11:55:14.005-06:00  INFO 23656 --- [           main] liquibase.ui                             : Running Changeset: db/changelog/client/create-client-email-table.xml::create-client-email-table::Edward Jing
2025-11-26T11:55:14.009-06:00  INFO 23656 --- [           main] liquibase.changelog                      : Table CLIENT_EMAIL created
2025-11-26T11:55:14.013-06:00  INFO 23656 --- [           main] liquibase.changelog                      : Primary key added to CLIENT_EMAIL (CLIENT_ID, EMAIL_ADDRESS_TX)
2025-11-26T11:55:14.028-06:00  INFO 23656 --- [           main] liquibase.changelog                      : Foreign key constraint added to CLIENT_EMAIL (CLIENT_ID)
2025-11-26T11:55:14.029-06:00  INFO 23656 --- [           main] liquibase.changelog                      : ChangeSet db/changelog/client/create-client-email-table.xml::create-client-email-table::Edward Jing ran successfully in 23ms
2025-11-26T11:55:14.040-06:00  INFO 23656 --- [           main] liquibase.ui                             : Running Changeset: db/changelog/client/create-admin-query-list-table.xml::create-admin-query-list::Edward Jing
2025-11-26T11:55:14.078-06:00  INFO 23656 --- [           main] liquibase.changelog                      : Table ADMIN_QUERY_LIST created
2025-11-26T11:55:14.079-06:00  INFO 23656 --- [           main] liquibase.changelog                      : ChangeSet db/changelog/client/create-admin-query-list-table.xml::create-admin-query-list::Edward Jing ran successfully in 38ms
2025-11-26T11:55:14.101-06:00  INFO 23656 --- [           main] liquibase.util                           : UPDATE SUMMARY
2025-11-26T11:55:14.102-06:00  INFO 23656 --- [           main] liquibase.util                           : Run:                         10
2025-11-26T11:55:14.102-06:00  INFO 23656 --- [           main] liquibase.util                           : Previously run:               0
2025-11-26T11:55:14.102-06:00  INFO 23656 --- [           main] liquibase.util                           : Filtered out:                 0
2025-11-26T11:55:14.102-06:00  INFO 23656 --- [           main] liquibase.util                           : -------------------------------
2025-11-26T11:55:14.102-06:00  INFO 23656 --- [           main] liquibase.util                           : Total change sets:           10
2025-11-26T11:55:14.105-06:00  INFO 23656 --- [           main] liquibase.util                           : Update summary generated
2025-11-26T11:55:14.113-06:00  INFO 23656 --- [           main] liquibase.command                        : Update command completed successfully.
2025-11-26T11:55:14.114-06:00  INFO 23656 --- [           main] liquibase.ui                             : Liquibase: Update has been successful. Rows affected: 10
2025-11-26T11:55:14.120-06:00  INFO 23656 --- [           main] liquibase.lockservice                    : Successfully released change log lock
2025-11-26T11:55:14.122-06:00  INFO 23656 --- [           main] liquibase.command                        : Command execution complete
2025-11-26T11:55:15.777-06:00  INFO 23656 --- [           main] o.hibernate.jpa.internal.util.LogHelper  : HHH000204: Processing PersistenceUnitInfo [name: default]
2025-11-26T11:55:15.944-06:00  INFO 23656 --- [           main] org.hibernate.Version                    : HHH000412: Hibernate ORM core version 6.6.15.Final
2025-11-26T11:55:16.028-06:00  INFO 23656 --- [           main] o.h.c.internal.RegionFactoryInitiator    : HHH000026: Second-level cache disabled
2025-11-26T11:55:16.265-06:00  INFO 23656 --- [           main] o.s.o.j.p.SpringPersistenceUnitInfo      : No LoadTimeWeaver setup: ignoring JPA class transformer
2025-11-26T11:55:16.368-06:00  INFO 23656 --- [           main] org.hibernate.orm.connections.pooling    : HHH10001005: Database info:
        Database JDBC URL [Connecting through datasource 'HikariDataSource (HikariPool-1)']
        Database driver: undefined/unknown
        Database version: 2.3.232
        Autocommit mode: undefined/unknown
        Isolation level: undefined/unknown
        Minimum pool size: undefined/unknown
        Maximum pool size: undefined/unknown
2025-11-26T11:55:16.753-06:00 ERROR 23656 --- [           main] j.LocalContainerEntityManagerFactoryBean : Failed to initialize JPA EntityManagerFactory: Referenced column 'sys_prin' mapped by target property 'id' occurs out of order in the list of '@JoinColumn's
2025-11-26T11:55:16.753-06:00  WARN 23656 --- [           main] s.c.a.AnnotationConfigApplicationContext : Exception encountered during context initialization - cancelling refresh attempt: org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'entityManagerFactory' defined in class path resource [org/springframework/boot/autoconfigure/orm/jpa/HibernateJpaConfiguration.class]: Referenced column 'sys_prin' mapped by target property 'id' occurs out of order in the list of '@JoinColumn's
2025-11-26T11:55:16.754-06:00  INFO 23656 --- [           main] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Shutdown initiated...
2025-11-26T11:55:16.757-06:00  INFO 23656 --- [           main] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Shutdown completed.
[ERROR] Tests run: 1, Failures: 0, Errors: 1, Skipped: 0, Time elapsed: 6.371 s <<< FAILURE! -- in rapid.model.LiquibaseValidationTest
[ERROR] rapid.model.LiquibaseValidationTest.liquibaseShouldCreateAllTables -- Time elapsed: 6.360 s <<< ERROR!
java.lang.IllegalStateException: Unstarted application context org.springframework.boot.test.context.assertj.AssertableApplicationContext[startupFailure=org.springframework.beans.factory.BeanCreationException] failed to start
        at org.springframework.boot.test.context.assertj.AssertProviderApplicationContextInvocationHandler.getStartedApplicationContext(AssertProviderApplicationContextInvocationHandler.java:156)
        at org.springframework.boot.test.context.assertj.AssertProviderApplicationContextInvocationHandler.invokeApplicationContextMethod(AssertProviderApplicationContextInvocationHandler.java:147)
        at org.springframework.boot.test.context.assertj.AssertProviderApplicationContextInvocationHandler.invoke(AssertProviderApplicationContextInvocationHandler.java:85)
        at jdk.proxy2/jdk.proxy2.$Proxy134.getBean(Unknown Source)
        at rapid.model.LiquibaseValidationTest.lambda$liquibaseShouldCreateAllTables$0(LiquibaseValidationTest.java:40)
        at org.springframework.boot.test.context.runner.AbstractApplicationContextRunner.accept(AbstractApplicationContextRunner.java:469)
        at org.springframework.boot.test.context.runner.AbstractApplicationContextRunner.consumeAssertableContext(AbstractApplicationContextRunner.java:385)
        at org.springframework.boot.test.context.runner.AbstractApplicationContextRunner.lambda$run$0(AbstractApplicationContextRunner.java:363)
        at org.springframework.boot.test.util.TestPropertyValues.lambda$applyToSystemProperties$1(TestPropertyValues.java:174)
        at org.springframework.boot.test.util.TestPropertyValues.applyToSystemProperties(TestPropertyValues.java:188)
        at org.springframework.boot.test.util.TestPropertyValues.applyToSystemProperties(TestPropertyValues.java:173)
        at org.springframework.boot.test.context.runner.AbstractApplicationContextRunner.lambda$run$1(AbstractApplicationContextRunner.java:363)
        at org.springframework.boot.test.context.runner.AbstractApplicationContextRunner.withContextClassLoader(AbstractApplicationContextRunner.java:391)
        at org.springframework.boot.test.context.runner.AbstractApplicationContextRunner.run(AbstractApplicationContextRunner.java:362)
        at rapid.model.LiquibaseValidationTest.liquibaseShouldCreateAllTables(LiquibaseValidationTest.java:39)
        at java.base/java.lang.reflect.Method.invoke(Method.java:565)
        at java.base/java.util.ArrayList.forEach(ArrayList.java:1604)
        at java.base/java.util.ArrayList.forEach(ArrayList.java:1604)
Caused by: org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'entityManagerFactory' defined in class path resource [org/springframework/boot/autoconfigure/orm/jpa/HibernateJpaConfiguration.class]: Referenced column 'sys_prin' mapped by target property 'id' occurs out of order in the list of '@JoinColumn's
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.initializeBean(AbstractAutowireCapableBeanFactory.java:1826)
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.doCreateBean(AbstractAutowireCapableBeanFactory.java:607)
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBean(AbstractAutowireCapableBeanFactory.java:529)
        at org.springframework.beans.factory.support.AbstractBeanFactory.lambda$doGetBean$0(AbstractBeanFactory.java:339)
        at org.springframework.beans.factory.support.DefaultSingletonBeanRegistry.getSingleton(DefaultSingletonBeanRegistry.java:373)
        at org.springframework.beans.factory.support.AbstractBeanFactory.doGetBean(AbstractBeanFactory.java:337)
        at org.springframework.beans.factory.support.AbstractBeanFactory.getBean(AbstractBeanFactory.java:207)
        at org.springframework.context.support.AbstractApplicationContext.finishBeanFactoryInitialization(AbstractApplicationContext.java:970)
        at org.springframework.context.support.AbstractApplicationContext.refresh(AbstractApplicationContext.java:627)
        at org.springframework.boot.test.context.runner.AbstractApplicationContextRunner.configureContext(AbstractApplicationContextRunner.java:456)
        at org.springframework.boot.test.context.runner.AbstractApplicationContextRunner.createAndLoadContext(AbstractApplicationContextRunner.java:426)
        at org.springframework.boot.test.context.runner.AbstractApplicationContextRunner.lambda$createAssertableContext$4(AbstractApplicationContextRunner.java:411)
        at org.springframework.boot.test.context.assertj.AssertProviderApplicationContextInvocationHandler.getContextOrStartupFailure(AssertProviderApplicationContextInvocationHandler.java:61)
        at org.springframework.boot.test.context.assertj.AssertProviderApplicationContextInvocationHandler.<init>(AssertProviderApplicationContextInvocationHandler.java:48)
        at org.springframework.boot.test.context.assertj.ApplicationContextAssertProvider.get(ApplicationContextAssertProvider.java:135)
        at org.springframework.boot.test.context.runner.AbstractApplicationContextRunner.createAssertableContext(AbstractApplicationContextRunner.java:411)
        at org.springframework.boot.test.context.runner.AbstractApplicationContextRunner.consumeAssertableContext(AbstractApplicationContextRunner.java:384)
        ... 11 more
Caused by: org.hibernate.AnnotationException: Referenced column 'sys_prin' mapped by target property 'id' occurs out of order in the list of '@JoinColumn's
        at org.hibernate.boot.model.internal.BinderHelper.findPropertiesByColumns(BinderHelper.java:544)
        at org.hibernate.boot.model.internal.BinderHelper.createSyntheticPropertyReference(BinderHelper.java:160)
        at org.hibernate.boot.model.internal.ToOneFkSecondPass.doSecondPass(ToOneFkSecondPass.java:114)
        at org.hibernate.boot.internal.InFlightMetadataCollectorImpl.processEndOfQueue(InFlightMetadataCollectorImpl.java:1937)
        at org.hibernate.boot.internal.InFlightMetadataCollectorImpl.processFkSecondPassesInOrder(InFlightMetadataCollectorImpl.java:1886)
        at org.hibernate.boot.internal.InFlightMetadataCollectorImpl.processSecondPasses(InFlightMetadataCollectorImpl.java:1794)
        at org.hibernate.boot.model.process.spi.MetadataBuildingProcess.complete(MetadataBuildingProcess.java:334)
        at org.hibernate.jpa.boot.internal.EntityManagerFactoryBuilderImpl.metadata(EntityManagerFactoryBuilderImpl.java:1442)
        at org.hibernate.jpa.boot.internal.EntityManagerFactoryBuilderImpl.build(EntityManagerFactoryBuilderImpl.java:1513)
        at org.springframework.orm.jpa.vendor.SpringHibernateJpaPersistenceProvider.createContainerEntityManagerFactory(SpringHibernateJpaPersistenceProvider.java:66)
        at org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean.createNativeEntityManagerFactory(LocalContainerEntityManagerFactoryBean.java:390)
        at org.springframework.orm.jpa.AbstractEntityManagerFactoryBean.buildNativeEntityManagerFactory(AbstractEntityManagerFactoryBean.java:419)
        at org.springframework.orm.jpa.AbstractEntityManagerFactoryBean.afterPropertiesSet(AbstractEntityManagerFactoryBean.java:400)
        at org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean.afterPropertiesSet(LocalContainerEntityManagerFactoryBean.java:366)
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.invokeInitMethods(AbstractAutowireCapableBeanFactory.java:1873)
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.initializeBean(AbstractAutowireCapableBeanFactory.java:1822)
        ... 27 more

[INFO] 
[INFO] Results:
[INFO] 
[ERROR] Errors: 
[ERROR]   LiquibaseValidationTest.liquibaseShouldCreateAllTables:39->lambda$liquibaseShouldCreateAllTables$0:40 ┬╗ IllegalState Unstarted application context org.springframework.boot.test.context.assertj.AssertableApplicationContext[startupFailure=org.springframework.beans.factory.BeanCreationException] failed to start                                        
[INFO] 
[ERROR] Tests run: 3, Failures: 0, Errors: 1, Skipped: 0
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] Reactor Summary for trace-client-sysprin-service 0.0.1-SNAPSHOT:
[INFO] 
[INFO] trace-client-sysprin-service ....................... SUCCESS [  0.357 s]
[INFO] common-model ....................................... FAILURE [ 25.838 s]
[INFO] common-api-dto ..................................... SKIPPED
[INFO] common-mapper ...................................... SKIPPED



