2025-08-12T14:01:37.417-05:00  INFO 18092 --- [client-sysprin-reader] [           main] .s.b.a.l.ConditionEvaluationReportLogger :

Error starting ApplicationContext. To display the condition evaluation report re-run your application with 'debug' enabled.
2025-08-12T14:01:37.532-05:00 ERROR 18092 --- [client-sysprin-reader] [           main] o.s.boot.SpringApplication               : Application run failed

org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'requestMappingHandlerMapping' defined in class path resource [org/springframework/boot/autoconfigure/web/servlet/WebMvcAutoConfiguration$EnableWebMvcConfiguration.class]: Ambiguous mapping. Cannot map 'clientLegacyController' method
rapid.client.web.ClientLegacyController#createClient(Client)
to {POST [/api/client/save]}: There is already 'clientController' bean method
rapid.client.web.ClientController#createClient(ClientDTO) mapped.
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.initializeBean(AbstractAutowireCapableBeanFactory.java:1826) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.doCreateBean(AbstractAutowireCapableBeanFactory.java:607) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBean(AbstractAutowireCapableBeanFactory.java:529) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.AbstractBeanFactory.lambda$doGetBean$0(AbstractBeanFactory.java:339) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.DefaultSingletonBeanRegistry.getSingleton(DefaultSingletonBeanRegistry.java:373) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.AbstractBeanFactory.doGetBean(AbstractBeanFactory.java:337) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.AbstractBeanFactory.getBean(AbstractBeanFactory.java:202) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.DefaultListableBeanFactory.instantiateSingleton(DefaultListableBeanFactory.java:1222) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.DefaultListableBeanFactory.preInstantiateSingleton(DefaultListableBeanFactory.java:1188) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.DefaultListableBeanFactory.preInstantiateSingletons(DefaultListableBeanFactory.java:1123) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.context.support.AbstractApplicationContext.finishBeanFactoryInitialization(AbstractApplicationContext.java:987) ~[spring-context-6.2.7.jar:6.2.7]
        at org.springframework.context.support.AbstractApplicationContext.refresh(AbstractApplicationContext.java:627) ~[spring-context-6.2.7.jar:6.2.7]
        at org.springframework.boot.web.servlet.context.ServletWebServerApplicationContext.refresh(ServletWebServerApplicationContext.java:146) ~[spring-boot-3.5.0.jar:3.5.0]
        at org.springframework.boot.SpringApplication.refresh(SpringApplication.java:753) ~[spring-boot-3.5.0.jar:3.5.0]
        at org.springframework.boot.SpringApplication.refreshContext(SpringApplication.java:439) ~[spring-boot-3.5.0.jar:3.5.0]
        at org.springframework.boot.SpringApplication.run(SpringApplication.java:318) ~[spring-boot-3.5.0.jar:3.5.0]
        at rapid.ClientSysPrinReaderApplication.main(ClientSysPrinReaderApplication.java:21) ~[classes/:na]
Caused by: java.lang.IllegalStateException: Ambiguous mapping. Cannot map 'clientLegacyController' method
rapid.client.web.ClientLegacyController#createClient(Client)
to {POST [/api/client/save]}: There is already 'clientController' bean method
rapid.client.web.ClientController#createClient(ClientDTO) mapped.
        at org.springframework.web.servlet.handler.AbstractHandlerMethodMapping$MappingRegistry.validateMethodMapping(AbstractHandlerMethodMapping.java:676) ~[spring-webmvc-6.2.7.jar:6.2.7]
        at org.springframework.web.servlet.handler.AbstractHandlerMethodMapping$MappingRegistry.register(AbstractHandlerMethodMapping.java:637) ~[spring-webmvc-6.2.7.jar:6.2.7]
        at org.springframework.web.servlet.handler.AbstractHandlerMethodMapping.registerHandlerMethod(AbstractHandlerMethodMapping.java:331) ~[spring-webmvc-6.2.7.jar:6.2.7]
        at org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping.registerHandlerMethod(RequestMappingHandlerMapping.java:507) ~[spring-webmvc-6.2.7.jar:6.2.7]
        at org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping.registerHandlerMethod(RequestMappingHandlerMapping.java:84) ~[spring-webmvc-6.2.7.jar:6.2.7]
        at org.springframework.web.servlet.handler.AbstractHandlerMethodMapping.lambda$detectHandlerMethods$2(AbstractHandlerMethodMapping.java:298) ~[spring-webmvc-6.2.7.jar:6.2.7]
        at java.base/java.util.LinkedHashMap.forEach(LinkedHashMap.java:987) ~[na:na]
        at org.springframework.web.servlet.handler.AbstractHandlerMethodMapping.detectHandlerMethods(AbstractHandlerMethodMapping.java:296) ~[spring-webmvc-6.2.7.jar:6.2.7]
        at org.springframework.web.servlet.handler.AbstractHandlerMethodMapping.processCandidateBean(AbstractHandlerMethodMapping.java:265) ~[spring-webmvc-6.2.7.jar:6.2.7]
        at org.springframework.web.servlet.handler.AbstractHandlerMethodMapping.initHandlerMethods(AbstractHandlerMethodMapping.java:224) ~[spring-webmvc-6.2.7.jar:6.2.7]
        at org.springframework.web.servlet.handler.AbstractHandlerMethodMapping.afterPropertiesSet(AbstractHandlerMethodMapping.java:212) ~[spring-webmvc-6.2.7.jar:6.2.7]
        at org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping.afterPropertiesSet(RequestMappingHandlerMapping.java:237) ~[spring-webmvc-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.invokeInitMethods(AbstractAutowireCapableBeanFactory.java:1873) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.initializeBean(AbstractAutowireCapableBeanFactory.java:1822) ~[spring-beans-6.2.7.jar:6.2.7]
        ... 16 common frames omitted
