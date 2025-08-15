2025-08-15T14:49:22.670-05:00  WARN 23456 --- [client-sysprin-reader] [           main] ConfigServletWebServerApplicationContext : Exception encountered during context initialization - cancelling refresh attempt: org.springframework.beans.factory.BeanDefinitionStoreException: Failed to parse configuration class [rapid.ClientSysPrinReaderApplication]
2025-08-15T14:49:22.948-05:00 ERROR 23456 --- [client-sysprin-reader] [           main] o.s.boot.SpringApplication               : Application run failed

org.springframework.beans.factory.BeanDefinitionStoreException: Failed to parse configuration class [rapid.ClientSysPrinReaderApplication]
        at org.springframework.context.annotation.ConfigurationClassParser.parse(ConfigurationClassParser.java:194) ~[spring-context-6.2.7.jar:6.2.7]
        at org.springframework.context.annotation.ConfigurationClassPostProcessor.processConfigBeanDefinitions(ConfigurationClassPostProcessor.java:418) ~[spring-context-6.2.7.jar:6.2.7]
        at org.springframework.context.annotation.ConfigurationClassPostProcessor.postProcessBeanDefinitionRegistry(ConfigurationClassPostProcessor.java:290) ~[spring-context-6.2.7.jar:6.2.7]
        at org.springframework.context.support.PostProcessorRegistrationDelegate.invokeBeanDefinitionRegistryPostProcessors(PostProcessorRegistrationDelegate.java:349) ~[spring-context-6.2.7.jar:6.2.7]
        at org.springframework.context.support.PostProcessorRegistrationDelegate.invokeBeanFactoryPostProcessors(PostProcessorRegistrationDelegate.java:118) ~[spring-context-6.2.7.jar:6.2.7]
        at org.springframework.context.support.AbstractApplicationContext.invokeBeanFactoryPostProcessors(AbstractApplicationContext.java:791) ~[spring-context-6.2.7.jar:6.2.7]
        at org.springframework.context.support.AbstractApplicationContext.refresh(AbstractApplicationContext.java:609) ~[spring-context-6.2.7.jar:6.2.7]
        at org.springframework.boot.web.servlet.context.ServletWebServerApplicationContext.refresh(ServletWebServerApplicationContext.java:146) ~[spring-boot-3.5.0.jar:3.5.0]
        at org.springframework.boot.SpringApplication.refresh(SpringApplication.java:753) ~[spring-boot-3.5.0.jar:3.5.0]
        at org.springframework.boot.SpringApplication.refreshContext(SpringApplication.java:439) ~[spring-boot-3.5.0.jar:3.5.0]
        at org.springframework.boot.SpringApplication.run(SpringApplication.java:318) ~[spring-boot-3.5.0.jar:3.5.0]
        at rapid.ClientSysPrinReaderApplication.main(ClientSysPrinReaderApplication.java:21) ~[classes/:na]
Caused by: org.springframework.context.annotation.ConflictingBeanDefinitionException: Annotation-specified bean name 'sysPrinDataFetcher' for bean class [rapid.sysprin.service.fetch.SysPrinDataFetcher] conflicts with existing, non-compatible bean definition of same name and class [rapid.client.service.fetch.SysPrinDataFetcher]
        at org.springframework.context.annotation.ClassPathBeanDefinitionScanner.checkCandidate(ClassPathBeanDefinitionScanner.java:361) ~[spring-context-6.2.7.jar:6.2.7]
        at org.springframework.context.annotation.ClassPathBeanDefinitionScanner.doScan(ClassPathBeanDefinitionScanner.java:288) ~[spring-context-6.2.7.jar:6.2.7]
        at org.springframework.context.annotation.ComponentScanAnnotationParser.parse(ComponentScanAnnotationParser.java:128) ~[spring-context-6.2.7.jar:6.2.7]
        at org.springframework.context.annotation.ConfigurationClassParser.doProcessConfigurationClass(ConfigurationClassParser.java:346) ~[spring-context-6.2.7.jar:6.2.7]
        at org.springframework.context.annotation.ConfigurationClassParser.processConfigurationClass(ConfigurationClassParser.java:281) ~[spring-context-6.2.7.jar:6.2.7]
        at org.springframework.context.annotation.ConfigurationClassParser.parse(ConfigurationClassParser.java:204) ~[spring-context-6.2.7.jar:6.2.7]
        at org.springframework.context.annotation.ConfigurationClassParser.parse(ConfigurationClassParser.java:172) ~[spring-context-6.2.7.jar:6.2.7]
        ... 11 common frames omitted

[INFO] ------------------------------------------------------------------------
[INFO] BUILD FAILURE
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  21.918 s
[INFO] Finished at: 2025-08-15T14:49:23-05:00
[INFO] ------------------------------------------------------------------------
[ERROR] Failed to execute goal org.springframework.boot:spring-boot-maven-plugin:3.4.2:run (default-cli) on project client-sysprin-reader: Process terminated with exit code: 1 -> [Help 1]
