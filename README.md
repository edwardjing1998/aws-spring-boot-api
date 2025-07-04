        Isolation level: undefined/unknown
        Minimum pool size: undefined/unknown
        Maximum pool size: undefined/unknown
2025-07-04T15:04:55.653-05:00 ERROR 22516 --- [           main] j.LocalContainerEntityManagerFactoryBean : Failed to initialize JPA EntityManagerFactory: Association 'rapid.model.client.Client.sysPrins' targets the type 'rapid.model.sysprin.SysPrin' which does not belong to the same persistence unit
2025-07-04T15:04:55.655-05:00  WARN 22516 --- [           main] s.c.a.AnnotationConfigApplicationContext : Exception encountered during context initialization - 
cancelling refresh attempt: org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'entityManagerFactory' defined in class path r
esource [org/springframework/boot/autoconfigure/orm/jpa/HibernateJpaConfiguration.class]: Association 'rapid.model.client.Client.sysPrins' targets the type 'rapid.model.sysprin.SysPrin' which does not belong to the same persistence unit
2025-07-04T15:04:55.655-05:00  INFO 22516 --- [           main] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Shutdown initiated...
2025-07-04T15:04:55.658-05:00  INFO 22516 --- [           main] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Shutdown completed.
[ERROR] Tests run: 1, Failures: 0, Errors: 1, Skipped: 0, Time elapsed: 4.512 s <<< FAILURE! -- in rapid.model.client.LiquibaseValidationTest
[ERROR] rapid.model.client.LiquibaseValidationTest.liquibaseShouldCreateAllTables -- Time elapsed: 4.506 s <<< ERROR!
java.lang.IllegalStateException: Unstarted application context org.springframework.boot.test.context.assertj.AssertableApplicationContext[startupFailure=org.springframework.beans.factory.BeanCreationException] failed to start
        at org.springframework.boot.test.context.assertj.AssertProviderApplicationContextInvocationHandler.getStartedApplicationContext(AssertProviderApplicationContextInvocationHandler.java:156)
        at org.springframework.boot.test.context.assertj.AssertProviderApplicationContextInvocationHandler.invokeApplicationContextMethod(AssertProviderApplicationContextInvocationHandler.java:147)
        at org.springframework.boot.test.context.assertj.AssertProviderApplicationContextInvocationHandler.invoke(AssertProviderApplicationContextInvocationHandler.java:85)
        at jdk.proxy2/jdk.proxy2.$Proxy134.getBean(Unknown Source)
        at rapid.model.client.LiquibaseValidationTest.lambda$liquibaseShouldCreateAllTables$0(LiquibaseValidationTest.java:40)
        at org.springframework.boot.test.context.runner.AbstractApplicationContextRunner.accept(AbstractApplicationContextRunner.java:469)
        at org.springframework.boot.test.context.runner.AbstractApplicationContextRunner.consumeAssertableContext(AbstractApplicationContextRunner.java:385)     
        at org.springframework.boot.test.context.runner.AbstractApplicationContextRunner.lambda$run$0(AbstractApplicationContextRunner.java:363)
        at org.springframework.boot.test.util.TestPropertyValues.lambda$applyToSystemProperties$1(TestPropertyValues.java:174)
        at org.springframework.boot.test.util.TestPropertyValues.applyToSystemProperties(TestPropertyValues.java:188)
        at org.springframework.boot.test.util.TestPropertyValues.applyToSystemProperties(TestPropertyValues.java:173)
        at org.springframework.boot.test.context.runner.AbstractApplicationContextRunner.lambda$run$1(AbstractApplicationContextRunner.java:363)
        at org.springframework.boot.test.context.runner.AbstractApplicationContextRunner.withContextClassLoader(AbstractApplicationContextRunner.java:391)       
        at org.springframework.boot.test.context.runner.AbstractApplicationContextRunner.run(AbstractApplicationContextRunner.java:362)
        at rapid.model.client.LiquibaseValidationTest.liquibaseShouldCreateAllTables(LiquibaseValidationTest.java:39)
        at java.base/java.lang.reflect.Method.invoke(Method.java:565)
        at java.base/java.util.ArrayList.forEach(ArrayList.java:1604)
        at java.base/java.util.ArrayList.forEach(ArrayList.java:1604)
Caused by: org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'entityManagerFactory' defined in class path resource [org/spri
ngframework/boot/autoconfigure/orm/jpa/HibernateJpaConfiguration.class]: Association 'rapid.model.client.Client.sysPrins' targets the type 'rapid.model.sysprin.SysPrin' which does not belong to the same persistence unit
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
Caused by: org.hibernate.AnnotationException: Association 'rapid.model.client.Client.sysPrins' targets the type 'rapid.model.sysprin.SysPrin' which does not belong to the same persistence unit
        at org.hibernate.boot.model.internal.CollectionBinder.detectManyToManyProblems(CollectionBinder.java:2610)
        at org.hibernate.boot.model.internal.CollectionBinder.bindManyToManySecondPass(CollectionBinder.java:2175)
        at org.hibernate.boot.model.internal.CollectionBinder.bindStarToManySecondPass(CollectionBinder.java:1620)
        at org.hibernate.boot.model.internal.CollectionBinder$1.secondPass(CollectionBinder.java:1604)
        at org.hibernate.boot.model.internal.CollectionSecondPass.doSecondPass(CollectionSecondPass.java:45)
        at org.hibernate.boot.internal.InFlightMetadataCollectorImpl.processSecondPasses(InFlightMetadataCollectorImpl.java:1842)
        at org.hibernate.boot.internal.InFlightMetadataCollectorImpl.processSecondPasses(InFlightMetadataCollectorImpl.java:1800)
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
[INFO] Reactor Summary:
[INFO]
[INFO] trace-client-sysprin-service 1.0-SNAPSHOT .......... SUCCESS [  0.414 s]
[INFO] client-sysprin-writer 1.0-SNAPSHOT ................. SUCCESS [  2.416 s]
[INFO] client-sysprin-reader 1.0-SNAPSHOT ................. SUCCESS [  0.155 s]
[INFO] common-model 0.0.1-SNAPSHOT ........................ FAILURE [ 17.779 s]
[INFO] common-mapper 1.0-SNAPSHOT ......................... SKIPPED
[INFO] ------------------------------------------------------------------------
[INFO] BUILD FAILURE
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  21.592 s
[INFO] Finished at: 2025-07-04T15:04:56-05:00
[INFO] ------------------------------------------------------------------------
[ERROR] Failed to execute goal org.apache.maven.plugins:maven-surefire-plugin:3.2.2:test (default-test) on project common-model:
[ERROR]
[ERROR] Please refer to C:\Users\F2LIPBX\spring_boot\trace-client-sysprin-service\common-model\target\surefire-reports for the individual test results.
[ERROR] Please refer to dump files (if any exist) [date].dump, [date]-jvmRun[N].dump and [date].dumpstream.
[ERROR] -> [Help 1]
[ERROR]
[ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
[ERROR] Re-run Maven using the -X switch to enable full debug logging.
[ERROR]
[ERROR] For more information about the errors and possible solutions, please read the following articles:
[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/MojoFailureException
[ERROR]
[ERROR] After correcting the problems, you can resume the build with the command
[ERROR]   mvn <args> -rf :common-model
PS C:\Users\F2LIPBX\spring_boot\trace-client-sysprin-service> 

