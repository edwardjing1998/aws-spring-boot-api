erties.PropertyMappingContextCustomizer@e2a3a8ce, org.springframework.boot.test.autoconfigure.web.servlet.WebDriverContextCustomizer@5f9edf14, org.springframework.test.context.support.DynamicPropertiesContextCustomizer@0, org.springframework.boot.test.context.SpringBootTestAnnotation@e2fd8762], contextLoader = org.springframework.boot.test.context.SpringBootContextLoader, parent = null]
        at org.springframework.test.context.cache.DefaultCacheAwareContextLoaderDelegate.loadContext(DefaultCacheAwareContextLoaderDelegate.java:145)
        at org.springframework.test.context.support.DefaultTestContext.getApplicationContext(DefaultTestContext.java:130)
        at org.springframework.test.context.support.DependencyInjectionTestExecutionListener.injectDependencies(DependencyInjectionTestExecutionListener.java:155)
        at org.springframework.test.context.support.DependencyInjectionTestExecutionListener.prepareTestInstance(DependencyInjectionTestExecutionListener.java:111)
        at org.springframework.test.context.TestContextManager.prepareTestInstance(TestContextManager.java:260)
        at org.springframework.test.context.junit.jupiter.SpringExtension.postProcessTestInstance(SpringExtension.java:159)
        at java.base/java.util.stream.ForEachOps$ForEachOp$OfRef.accept(ForEachOps.java:186)
        at java.base/java.util.stream.ReferencePipeline$3$1.accept(ReferencePipeline.java:214)
        at java.base/java.util.stream.ReferencePipeline$2$1.accept(ReferencePipeline.java:197)
        at java.base/java.util.stream.ReferencePipeline$3$1.accept(ReferencePipeline.java:214)
        at java.base/java.util.ArrayList$ArrayListSpliterator.forEachRemaining(ArrayList.java:1716)
        at java.base/java.util.stream.AbstractPipeline.copyInto(AbstractPipeline.java:570)
        at java.base/java.util.stream.AbstractPipeline.wrapAndCopyInto(AbstractPipeline.java:560)
        at java.base/java.util.stream.ForEachOps$ForEachOp.evaluateSequential(ForEachOps.java:153)
        at java.base/java.util.stream.ForEachOps$ForEachOp$OfRef.evaluateSequential(ForEachOps.java:176)
        at java.base/java.util.stream.AbstractPipeline.evaluate(AbstractPipeline.java:265)
        at java.base/java.util.stream.ReferencePipeline.forEach(ReferencePipeline.java:632)
        at java.base/java.util.Optional.orElseGet(Optional.java:364)
        at java.base/java.util.ArrayList.forEach(ArrayList.java:1604)
        at java.base/java.util.ArrayList.forEach(ArrayList.java:1604)

[INFO] Running rapid.model.cases.LiquibaseValidationTest
2025-10-17T10:27:18.060-05:00  INFO 34936 --- [           main] .s.d.r.c.RepositoryConfigurationDelegate : Bootstrapping Spring Data JPA repositories in DEFAULT mode.
2025-10-17T10:27:18.113-05:00  INFO 34936 --- [           main] .s.d.r.c.RepositoryConfigurationDelegate : Finished Spring Data repository scanning in 51 ms. Found 0 JPA repository interfaces.
2025-10-17T10:27:18.827-05:00  INFO 34936 --- [           main] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Starting...
2025-10-17T10:27:19.336-05:00  INFO 34936 --- [           main] com.zaxxer.hikari.pool.HikariPool        : HikariPool-1 - Added connection conn2: url=jdbc:h2:mem:testdb user=SA
2025-10-17T10:27:19.346-05:00  INFO 34936 --- [           main] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Start completed.
2025-10-17T10:27:23.455-05:00  INFO 34936 --- [           main] o.hibernate.jpa.internal.util.LogHelper  : HHH000204: Processing PersistenceUnitInfo [name: default]
2025-10-17T10:27:23.480-05:00  INFO 34936 --- [           main] o.h.c.internal.RegionFactoryInitiator    : HHH000026: Second-level cache disabled
2025-10-17T10:27:23.596-05:00  INFO 34936 --- [           main] o.s.o.j.p.SpringPersistenceUnitInfo      : No LoadTimeWeaver setup: ignoring JPA class transformer
2025-10-17T10:27:23.705-05:00  INFO 34936 --- [           main] org.hibernate.orm.connections.pooling    : HHH10001005: Database info:
        Database JDBC URL [Connecting through datasource 'HikariDataSource (HikariPool-1)']
        Database driver: undefined/unknown
        Database version: 2.3.232
        Autocommit mode: undefined/unknown
        Isolation level: undefined/unknown
        Minimum pool size: undefined/unknown
        Maximum pool size: undefined/unknown
2025-10-17T10:27:27.056-05:00  INFO 34936 --- [           main] o.h.e.t.j.p.i.JtaPlatformInitiator       : HHH000489: No JTA platform available (set 'hibernate.transaction.jta.platform' to enable JTA platform integration)
2025-10-17T10:27:27.062-05:00  INFO 34936 --- [           main] j.LocalContainerEntityManagerFactoryBean : Initialized JPA EntityManagerFactory for persistence unit 'default'
2025-10-17T10:27:27.655-05:00  INFO 34936 --- [           main] j.LocalContainerEntityManagerFactoryBean : Closing JPA EntityManagerFactory for persistence unit 'default'
2025-10-17T10:27:27.658-05:00  INFO 34936 --- [           main] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Shutdown initiated...
2025-10-17T10:27:27.662-05:00  INFO 34936 --- [           main] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Shutdown completed.
[INFO] Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 10.40 s -- in rapid.model.cases.LiquibaseValidationTest
[INFO] 
[INFO] Results:
[INFO] 
[ERROR] Errors: 
[ERROR]   DltCaseAddressMappingTest.caseAndAddress_arePersistedAndLinked ┬╗ IllegalState ApplicationContext failure threshold (1) exceeded: skipping repeated attempt to load context for [MergedContextConfiguration@6ffbf0ac testClass = rapid.model.cases.DltCaseAddressMappingTest, locations = [], classes = [rapid.model.cases.TestApp], contextInitializerClasses = [], activeProfiles = ["test"], propertySourceDescriptors = [PropertySourceDescriptor[locations=[], ignoreResourceNotFound=false, name=null, propertySourceFactory=null, encoding=null]], propertySourceProperties = ["spring.liquibase.enabled=false", "spring.jpa.hibernate.ddl-auto=create-drop", "spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.H2Dialect", "spring.datasource.url=jdbc:h2:mem:cases;MODE=MSSQLServer;DB_CLOSE_DELAY=-1;DATABASE_TO_LOWER=TRUE", "spring.datasource.driverClassName=org.h2.Driver", "spring.datasource.username=sa", "spring.datasource.password=", "org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTestContextBootstrapper=true"], contextCustomizers = [[ImportsContextCustomizer@18ac4af6 key = [@org.springframework.boot.test.autoconfigure.orm.jpa.AutoConfigureDataJpa(), @org.springframework.boot.test.autoconfigure.orm.jpa.AutoConfigureTestEntityManager(), @org.springframework.test.context.ActiveProfiles(inheritProfiles=true, profiles={"test"}, resolver=org.springframework.test.context.ActiveProfilesResolver.class, value={"test"}), @org.springframework.boot.test.autoconfigure.filter.TypeExcludeFilters(value={org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTypeExcludeFilter.class}), @org.springframework.context.annotation.Import(value={org.springframework.boot.autoconfigure.domain.EntityScanPackages.Registrar.class}), @org.springframework.context.annotation.Import(value={org.springframework.boot.autoconfigure.ImportAutoConfigurationImportSelector.class}), @org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest(bootstrapMode=DEFAULT, excludeAutoConfiguration={}, excludeFilters={}, includeFilters={}, properties={}, showSql=true, useDefaultFilters=true), @org.springframework.boot.test.autoconfigure.core.AutoConfigureCache(cacheProvider=NONE), @org.springframework.boot.test.autoconfigure.jdbc.AutoConfigureTestDatabase(connection=NONE, replace=NON_TEST), @org.springframework.test.context.TestPropertySource(encoding="", factory=org.springframework.core.io.support.PropertySourceFactory.class, inheritLocations=true, inheritProperties=true, locations={}, properties={"spring.liquibase.enabled=false", "spring.jpa.hibernate.ddl-auto=create-drop", "spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.H2Dialect", "spring.datasource.url=jdbc:h2:mem:cases;MODE=MSSQLServer;DB_CLOSE_DELAY=-1;DATABASE_TO_LOWER=TRUE", "spring.datasource.driverClassName=org.h2.Driver", "spring.datasource.username=sa", "spring.datasource.password="}, value={}), @org.springframework.boot.test.autoconfigure.jdbc.AutoConfigureTestDatabase(replace=ANY, connection=NONE), @org.springframework.test.context.BootstrapWith(value=org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTestContextBootstrapper.class), @org.springframework.boot.autoconfigure.ImportAutoConfiguration(classes={}, exclude={}, value={}), @org.springframework.boot.autoconfigure.domain.EntityScan(basePackageClasses={rapid.model.cases.cases.Case.class, rapid.model.cases.address.Address.class}, basePackages={}, value={}), @org.springframework.context.annotation.Import(value={org.springframework.data.jpa.repository.config.JpaRepositoriesRegistrar.class}), @org.springframework.transaction.annotation.Transactional(isolation=DEFAULT, label={}, noRollbackFor={}, noRollbackForClassName={}, propagation=REQUIRED, readOnly=false, rollbackFor={}, rollbackForClassName={}, timeout=-1, timeoutString="", transactionManager="", value=""), @org.apiguardian.api.API(consumers={"*"}, since="5.0", status=STABLE), @org.springframework.data.jpa.repository.config.EnableJpaRepositories(basePackageClasses={rapid.repository.cases.cases.CaseRepo.class, rapid.repository.cases.address.AddressRepo.class}, basePackages={}, bootstrapMode=DEFAULT, considerNestedRepositories=false, enableDefaultTransactions=true, entityManagerFactoryRef="entityManagerFactory", escapeCharacter='\', excludeFilters={}, includeFilters={}, nameGenerator=org.springframework.beans.factory.support.BeanNameGenerator.class, namedQueriesLocation="", queryLookupStrategy=CREATE_IF_NOT_FOUND, repositoryBaseClass=org.springframework.data.repository.config.DefaultRepositoryBaseClass.class, repositoryFactoryBeanClass=org.springframework.data.jpa.repository.support.JpaRepositoryFactoryBean.class, repositoryImplementationPostfix="Impl", transactionManagerRef="transactionManager", value={}), @org.springframework.boot.test.autoconfigure.OverrideAutoConfiguration(enabled=false), @org.springframework.aot.hint.annotation.Reflective(processors={org.springframework.aot.hint.annotation.SimpleReflectiveProcessor.class}, value={org.springframework.aot.hint.annotation.SimpleReflectiveProcessor.class}), @org.springframework.boot.test.autoconfigure.properties.PropertyMapping(skip=NO, value="spring.test.database")]], org.springframework.boot.test.context.filter.ExcludeFilterContextCustomizer@24c1b2d2, org.springframework.boot.test.json.DuplicateJsonObjectContextCustomizerFactory$DuplicateJsonObjectContextCustomizer@465232e9, org.springframework.boot.test.mock.mockito.MockitoContextCustomizer@0, org.springframework.boot.test.web.reactor.netty.DisableReactorResourceFactoryGlobalResourcesContextCustomizerFactory$DisableReactorResourceFactoryGlobalResourcesContextCustomizerCustomizer@3bcd05cb, org.springframework.boot.test.autoconfigure.OnFailureConditionReportContextCustomizerFactory$OnFailureConditionReportContextCustomizer@20bd8be5, org.springframework.boot.test.autoconfigure.OverrideAutoConfigurationContextCustomizerFactory$DisableAutoConfigurationContextCustomizer@3456558, org.springframework.boot.test.autoconfigure.actuate.observability.ObservabilityContextCustomizerFactory$DisableObservabilityContextCustomizer@1f, org.springframework.boot.test.autoconfigure.filter.TypeExcludeFiltersContextCustomizer@34be3d80, org.springframework.boot.test.autoconfigure.properties.PropertyMappingContextCustomizer@e2a3a8ce, org.springframework.boot.test.autoconfigure.web.servlet.WebDriverContextCustomizer@5f9edf14, org.springframework.test.context.support.DynamicPropertiesContextCustomizer@0, org.springframework.boot.test.context.SpringBootTestAnnotation@e2fd8762], contextLoader = org.springframework.boot.test.context.SpringBootContextLoader, parent = null]                                                                                                    
[ERROR]   DltCaseAddressMappingTest.repoMethod_isCovered ┬╗ IllegalState Failed to load ApplicationContext for [MergedContextConfiguration@6ffbf0ac testClass = rapid.model.cases.DltCaseAddressMappingTest, locations = [], classes = [rapid.model.cases.TestApp], contextInitializerClasses = [], activeProfiles = ["test"], propertySourceDescriptors = [PropertySourceDescriptor[locations=[], ignoreResourceNotFound=false, name=null, propertySourceFactory=null, encoding=null]], propertySourceProperties = ["spring.liquibase.enabled=false", "spring.jpa.hibernate.ddl-auto=create-drop", "spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.H2Dialect", "spring.datasource.url=jdbc:h2:mem:cases;MODE=MSSQLServer;DB_CLOSE_DELAY=-1;DATABASE_TO_LOWER=TRUE", "spring.datasource.driverClassName=org.h2.Driver", "spring.datasource.username=sa", "spring.datasource.password=", "org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTestContextBootstrapper=true"], contextCustomizers = [[ImportsContextCustomizer@18ac4af6 key = [@org.springframework.boot.test.autoconfigure.orm.jpa.AutoConfigureDataJpa(), @org.springframework.boot.test.autoconfigure.orm.jpa.AutoConfigureTestEntityManager(), @org.springframework.test.context.ActiveProfiles(inheritProfiles=true, profiles={"test"}, resolver=org.springframework.test.context.ActiveProfilesResolver.class, value={"test"}), @org.springframework.boot.test.autoconfigure.filter.TypeExcludeFilters(value={org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTypeExcludeFilter.class}), @org.springframework.context.annotation.Import(value={org.springframework.boot.autoconfigure.domain.EntityScanPackages.Registrar.class}), @org.springframework.context.annotation.Import(value={org.springframework.boot.autoconfigure.ImportAutoConfigurationImportSelector.class}), @org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest(bootstrapMode=DEFAULT, excludeAutoConfiguration={}, excludeFilters={}, includeFilters={}, properties={}, showSql=true, useDefaultFilters=true), @org.springframework.boot.test.autoconfigure.core.AutoConfigureCache(cacheProvider=NONE), @org.springframework.boot.test.autoconfigure.jdbc.AutoConfigureTestDatabase(connection=NONE, replace=NON_TEST), @org.springframework.test.context.TestPropertySource(encoding="", factory=org.springframework.core.io.support.PropertySourceFactory.class, inheritLocations=true, inheritProperties=true, locations={}, properties={"spring.liquibase.enabled=false", "spring.jpa.hibernate.ddl-auto=create-drop", "spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.H2Dialect", "spring.datasource.url=jdbc:h2:mem:cases;MODE=MSSQLServer;DB_CLOSE_DELAY=-1;DATABASE_TO_LOWER=TRUE", "spring.datasource.driverClassName=org.h2.Driver", "spring.datasource.username=sa", "spring.datasource.password="}, value={}), @org.springframework.boot.test.autoconfigure.jdbc.AutoConfigureTestDatabase(replace=ANY, connection=NONE), @org.springframework.test.context.BootstrapWith(value=org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTestContextBootstrapper.class), @org.springframework.boot.autoconfigure.ImportAutoConfiguration(classes={}, exclude={}, value={}), @org.springframework.boot.autoconfigure.domain.EntityScan(basePackageClasses={rapid.model.cases.cases.Case.class, rapid.model.cases.address.Address.class}, basePackages={}, value={}), @org.springframework.context.annotation.Import(value={org.springframework.data.jpa.repository.config.JpaRepositoriesRegistrar.class}), @org.springframework.transaction.annotation.Transactional(isolation=DEFAULT, label={}, noRollbackFor={}, noRollbackForClassName={}, propagation=REQUIRED, readOnly=false, rollbackFor={}, rollbackForClassName={}, timeout=-1, timeoutString="", transactionManager="", value=""), @org.apiguardian.api.API(consumers={"*"}, since="5.0", status=STABLE), @org.springframework.data.jpa.repository.config.EnableJpaRepositories(basePackageClasses={rapid.repository.cases.cases.CaseRepo.class, rapid.repository.cases.address.AddressRepo.class}, basePackages={}, bootstrapMode=DEFAULT, considerNestedRepositories=false, enableDefaultTransactions=true, entityManagerFactoryRef="entityManagerFactory", escapeCharacter='\', excludeFilters={}, includeFilters={}, nameGenerator=org.springframework.beans.factory.support.BeanNameGenerator.class, namedQueriesLocation="", queryLookupStrategy=CREATE_IF_NOT_FOUND, repositoryBaseClass=org.springframework.data.repository.config.DefaultRepositoryBaseClass.class, repositoryFactoryBeanClass=org.springframework.data.jpa.repository.support.JpaRepositoryFactoryBean.class, repositoryImplementationPostfix="Impl", transactionManagerRef="transactionManager", value={}), @org.springframework.boot.test.autoconfigure.OverrideAutoConfiguration(enabled=false), @org.springframework.aot.hint.annotation.Reflective(processors={org.springframework.aot.hint.annotation.SimpleReflectiveProcessor.class}, value={org.springframework.aot.hint.annotation.SimpleReflectiveProcessor.class}), @org.springframework.boot.test.autoconfigure.properties.PropertyMapping(skip=NO, value="spring.test.database")]], org.springframework.boot.test.context.filter.ExcludeFilterContextCustomizer@24c1b2d2, org.springframework.boot.test.json.DuplicateJsonObjectContextCustomizerFactory$DuplicateJsonObjectContextCustomizer@465232e9, org.springframework.boot.test.mock.mockito.MockitoContextCustomizer@0, org.springframework.boot.test.web.reactor.netty.DisableReactorResourceFactoryGlobalResourcesContextCustomizerFactory$DisableReactorResourceFactoryGlobalResourcesContextCustomizerCustomizer@3bcd05cb, org.springframework.boot.test.autoconfigure.OnFailureConditionReportContextCustomizerFactory$OnFailureConditionReportContextCustomizer@20bd8be5, org.springframework.boot.test.autoconfigure.OverrideAutoConfigurationContextCustomizerFactory$DisableAutoConfigurationContextCustomizer@3456558, org.springframework.boot.test.autoconfigure.actuate.observability.ObservabilityContextCustomizerFactory$DisableObservabilityContextCustomizer@1f, org.springframework.boot.test.autoconfigure.filter.TypeExcludeFiltersContextCustomizer@34be3d80, org.springframework.boot.test.autoconfigure.properties.PropertyMappingContextCustomizer@e2a3a8ce, org.springframework.boot.test.autoconfigure.web.servlet.WebDriverContextCustomizer@5f9edf14, org.springframework.test.context.support.DynamicPropertiesContextCustomizer@0, org.springframework.boot.test.context.SpringBootTestAnnotation@e2fd8762], contextLoader = org.springframework.boot.test.context.SpringBootContextLoader, parent = null]                                                                                                                                                                               
[INFO] 
[ERROR] Tests run: 5, Failures: 0, Errors: 2, Skipped: 0
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] BUILD FAILURE
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  50.697 s
[INFO] Finished at: 2025-10-17T10:27:28-05:00
[INFO] ----------------------









package rapid.model.cases;

import org.junit.jupiter.api.Test;
import org.springframework.boot.test.context.runner.ApplicationContextRunner;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.test.context.ActiveProfiles;

import static org.assertj.core.api.Assertions.assertThat;

@ActiveProfiles("test")
class LiquibaseValidationTest {

    private final ApplicationContextRunner contextRunner = new ApplicationContextRunner()
            .withUserConfiguration(TestApp.class)  // <-- now referencing the extracted class
            .withPropertyValues(
                    "spring.datasource.url=jdbc:h2:mem:testdb;DB_CLOSE_DELAY=-1",
                    "spring.datasource.driver-class-name=org.h2.Driver",
                    "spring.datasource.username=sa",
                    "spring.datasource.password=",
                    "spring.liquibase.enabled=true",
                    "spring.liquibase.change-log=classpath:db/changelog/db.changelog-master.xml"
            );

    private final String[] expectedTables = {
            "DELETED_FAILED_TRANS",
            "DELETED_ACCOUNT_TRANS",
            "DELETED_ADDRESSES",
            "DELETED_BULK_CARD",
            "DELETED_CASES",
            "DELETED_LABELS",
            "DELETED_TRANSACTIONS"
    };

    @Test
    void liquibaseShouldCreateAllTables() {
        contextRunner.run(context -> {
            JdbcTemplate jdbcTemplate = context.getBean(JdbcTemplate.class);
            for (String table : expectedTables) {
                Boolean exists = jdbcTemplate.queryForObject(
                        "SELECT COUNT(*) > 0 FROM INFORMATION_SCHEMA.TABLES WHERE TABLE_NAME = ?",
                        Boolean.class,
                        table
                );
                assertThat(exists)
                        .as("Table " + table + " should exist")
                        .isTrue();
            }
        });
    }
}



package rapid.model.cases;

import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.test.context.ActiveProfiles;

import javax.sql.DataSource;

@SpringBootApplication
@ActiveProfiles("test")
public class TestApp {
    @Bean
    public JdbcTemplate jdbcTemplate(DataSource dataSource) {
        return new JdbcTemplate(dataSource);
    }
}
