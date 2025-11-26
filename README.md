2025-11-26T09:32:49.971-06:00 ERROR 20556 --- [client-sysprin-reader] [           main] j.LocalContainerEntityManagerFactoryBean : Failed to initialize JPA EntityManagerFactory: Referenced column 'sys_prin' mapped by target property 'id' occurs out of order in the list of '@JoinColumn's
2025-11-26T09:32:49.974-06:00  WARN 20556 --- [client-sysprin-reader] [           main] ConfigServletWebServerApplicationContext : Exception encountered during context initialization - cancelling refresh attempt: org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'entityManagerFactory' defined in class path resource [org/springframework/boot/autoconfigure/orm/jpa/HibernateJpaConfiguration.class]: Referenced column 'sys_prin' mapped by target property 'id' occurs out of order in the list of '@JoinColumn's
2025-11-26T09:32:49.975-06:00  INFO 20556 --- [client-sysprin-reader] [           main] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Shutdown initiated...
2025-11-26T09:32:50.249-06:00  INFO 20556 --- [client-sysprin-reader] [           main] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Shutdown completed.
2025-11-26T09:32:50.256-06:00  INFO 20556 --- [client-sysprin-reader] [           main] o.apache.catalina.core.StandardService   : Stopping service [Tomcat]
2025-11-26T09:32:50.283-06:00  INFO 20556 --- [client-sysprin-reader] [           main] .s.b.a.l.ConditionEvaluationReportLogger :

Error starting ApplicationContext. To display the condition evaluation report re-run your application with 'debug' enabled.
2025-11-26T09:32:50.315-06:00 ERROR 20556 --- [client-sysprin-reader] [           main] o.s.boot.SpringApplication               : Application run failed

org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'entityManagerFactory' defined in class path resource [org/springframework/boot/autoconfigure/orm/jpa/HibernateJpaConfiguration.class]: Referenced column 'sys_prin' mapped by target property 'id' occurs out of order in the list of '@JoinColumn's
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.initializeBean(AbstractAutowireCapableBeanFactory.java:1826) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.doCreateBean(AbstractAutowireCapableBeanFactory.java:607) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBean(AbstractAutowireCapableBeanFactory.java:529) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.AbstractBeanFactory.lambda$doGetBean$0(AbstractBeanFactory.java:339) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.DefaultSingletonBeanRegistry.getSingleton(DefaultSingletonBeanRegistry.java:373) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.AbstractBeanFactory.doGetBean(AbstractBeanFactory.java:337) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.AbstractBeanFactory.getBean(AbstractBeanFactory.java:207) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.context.support.AbstractApplicationContext.finishBeanFactoryInitialization(AbstractApplicationContext.java:970) ~[spring-context-6.2.7.jar:6.2.7]
        at org.springframework.context.support.AbstractApplicationContext.refresh(AbstractApplicationContext.java:627) ~[spring-context-6.2.7.jar:6.2.7]
        at org.springframework.boot.web.servlet.context.ServletWebServerApplicationContext.refresh(ServletWebServerApplicationContext.java:146) ~[spring-boot-3.5.0.jar:3.5.0]
        at org.springframework.boot.SpringApplication.refresh(SpringApplication.java:753) ~[spring-boot-3.5.0.jar:3.5.0]
        at org.springframework.boot.SpringApplication.refreshContext(SpringApplication.java:439) ~[spring-boot-3.5.0.jar:3.5.0]
        at org.springframework.boot.SpringApplication.run(SpringApplication.java:318) ~[spring-boot-3.5.0.jar:3.5.0]
        at rapid.reader.ClientSysPrinReaderApplication.main(ClientSysPrinReaderApplication.java:19) ~[classes/:na]
Caused by: org.hibernate.AnnotationException: Referenced column 'sys_prin' mapped by target property 'id' occurs out of order in the list of '@JoinColumn's
        at org.hibernate.boot.model.internal.BinderHelper.findPropertiesByColumns(BinderHelper.java:544) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.boot.model.internal.BinderHelper.createSyntheticPropertyReference(BinderHelper.java:160) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.boot.model.internal.ToOneFkSecondPass.doSecondPass(ToOneFkSecondPass.java:114) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.boot.internal.InFlightMetadataCollectorImpl.processEndOfQueue(InFlightMetadataCollectorImpl.java:1937) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.boot.internal.InFlightMetadataCollectorImpl.processFkSecondPassesInOrder(InFlightMetadataCollectorImpl.java:1886) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.boot.internal.InFlightMetadataCollectorImpl.processSecondPasses(InFlightMetadataCollectorImpl.java:1794) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.boot.model.process.spi.MetadataBuildingProcess.complete(MetadataBuildingProcess.java:334) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.jpa.boot.internal.EntityManagerFactoryBuilderImpl.metadata(EntityManagerFactoryBuilderImpl.java:1442) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.jpa.boot.internal.EntityManagerFactoryBuilderImpl.build(EntityManagerFactoryBuilderImpl.java:1513) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.springframework.orm.jpa.vendor.SpringHibernateJpaPersistenceProvider.createContainerEntityManagerFactory(SpringHibernateJpaPersistenceProvider.java:66) ~[spring-orm-6.2.7.jar:6.2.7]
        at org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean.createNativeEntityManagerFactory(LocalContainerEntityManagerFactoryBean.java:390) ~[spring-orm-6.2.7.jar:6.2.7]
        at org.springframework.orm.jpa.AbstractEntityManagerFactoryBean.buildNativeEntityManagerFactory(AbstractEntityManagerFactoryBean.java:419) ~[spring-orm-6.2.7.jar:6.2.7]
        at org.springframework.orm.jpa.AbstractEntityManagerFactoryBean.afterPropertiesSet(AbstractEntityManagerFactoryBean.java:400) ~[spring-orm-6.2.7.jar:6.2.7]
        at org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean.afterPropertiesSet(LocalContainerEntityManagerFactoryBean.java:366) ~[spring-orm-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.invokeInitMethods(AbstractAutowireCapableBeanFactory.java:1873) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.initializeBean(AbstractAutowireCapableBeanFactory.java:1822) ~[spring-beans-6.2.7.jar:6.2.7]
        ... 13 common frames omitted

[INFO] ------------------------------------------------------------------------
[INFO] BUILD FAILURE
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  27.583 s
[INFO] Finished at: 2025-11-26T09:32:50-06:00
[INFO] ------------------------------------------------------------------------
[ERROR] Failed to execute goal org.springframework.boot:spring-boot-maven-plugin:3.4.2:run (default-cli) on project client-sysprin-reader: Process terminated with exit code: 1 -> [Help 1]
[ERROR]
[ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
[ERROR] Re-run Maven using the -X switch to enable full debug logging.
[ERROR]
[ERROR] For more information about the errors and possible solutions, please read the following articles:
[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/MojoExecutionException

C:\Users\F2LIPBX\sp
