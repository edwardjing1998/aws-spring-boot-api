2025-10-19T12:06:54.994-05:00  INFO 17128 --- [client-sysprin-writer] [           main] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Shutdown initiated...
2025-10-19T12:06:55.940-05:00  INFO 17128 --- [client-sysprin-writer] [           main] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Shutdown completed.
2025-10-19T12:06:55.943-05:00  INFO 17128 --- [client-sysprin-writer] [           main] o.apache.catalina.core.StandardService   : Stopping service [Tomcat]
2025-10-19T12:06:55.971-05:00  INFO 17128 --- [client-sysprin-writer] [           main] .s.b.a.l.ConditionEvaluationReportLogger :

Error starting ApplicationContext. To display the condition evaluation report re-run your application with 'debug' enabled.
2025-10-19T12:06:56.007-05:00 ERROR 17128 --- [client-sysprin-writer] [           main] o.s.boot.SpringApplication               : Application run failed

org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating bean with name 'sysPrinChangeAllController' defined in file [C:\Users\F2LIPBX\spring_boot\harish\trace-clients\client-sysprin-writer\target\classes\rapid\sysprin\web\SysPrinChangeAllController.class]: Unsatisfied dependency expressed through constructor parameter 0: Error creating bean with name 'sysPrinChangeAllService' defined in URL [jar:file:/C:/Users/F2LIPBX/.m2/repository/trace-client-sysprin-service/common-services/0.0.1-SNAPSHOT/common-services-0.0.1-SNAPSHOT.jar!/rapid/service/sysprin/SysPrinChangeAllService.class]: Unsatisfied dependency expressed through constructor parameter 0: Error creating bean with name 'sysPrinNativeRepository' defined in rapid.repository.sysprin.SysPrinNativeRepository defined in @EnableJpaRepositories declared on ClientSysPrinWriterApplication: Not a managed type: class java.lang.Object
        at org.springframework.beans.factory.support.ConstructorResolver.createArgumentArray(ConstructorResolver.java:804) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.ConstructorResolver.autowireConstructor(ConstructorResolver.java:240) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.autowireConstructor(AbstractAutowireCapableBeanFactory.java:1395) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBeanInstance(AbstractAutowireCapableBeanFactory.java:1232) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.doCreateBean(AbstractAutowireCapableBeanFactory.java:569) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBean(AbstractAutowireCapableBeanFactory.java:529) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.AbstractBeanFactory.lambda$doGetBean$0(AbstractBeanFactory.java:339) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.DefaultSingletonBeanRegistry.getSingleton(DefaultSingletonBeanRegistry.java:373) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.AbstractBeanFactory.doGetBean(AbstractBeanFactory.java:337) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.AbstractBeanFactory.getBean(AbstractBeanFactory.java:202) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframe
