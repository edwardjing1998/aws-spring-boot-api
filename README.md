package rapid.searchintegration;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.autoconfigure.domain.EntityScan;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.FilterType;
import org.springframework.data.jpa.repository.config.EnableJpaRepositories;
import org.springframework.web.bind.annotation.CrossOrigin;

@CrossOrigin(origins = {"*"})
@SpringBootApplication
@EntityScan("rapid.model")
@EnableJpaRepositories(
        basePackages = "rapid",   excludeFilters = {
        // Exclude by concrete type(s)
        @ComponentScan.Filter(type = FilterType.ASSIGNABLE_TYPE, classes = {
                rapid.repository.client.ClientDetailDaoWithNativeSql.class,
                rapid.repository.sysprin.SysPrinDetailDaoWithNativeSql.class
        }),
})
public class SearchIntegrationApplication {

    public static void main(String[] args) {
        SpringApplication.run(SearchIntegrationApplication.class, args);
    }

}









package rapid.writer;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.autoconfigure.domain.EntityScan;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.FilterType;
import org.springframework.data.jpa.repository.config.EnableJpaRepositories;
import org.springframework.web.bind.annotation.CrossOrigin;

@CrossOrigin(origins = { "*" })
@SpringBootApplication
@EntityScan("rapid.model")
@EnableJpaRepositories(
        basePackages = "rapid",
        excludeFilters = {
        // Exclude by concrete type(s)
        @ComponentScan.Filter(type = FilterType.ASSIGNABLE_TYPE, classes = {
                rapid.repository.client.ClientDetailDaoWithNativeSql.class,
                rapid.repository.sysprin.SysPrinDetailDaoWithNativeSql.class
        }),
})
public class ClientSysPrinWriterApplication {

    public static void main(String[] args) {
        SpringApplication sa = new SpringApplication(ClientSysPrinWriterApplication.class);
        sa.run(args);
    }




    No operations defined in spec!
}



org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating bean with name 'sysPrinController' defined in file [C:\Users\F2LIPBX\spring_boot\harish\trace-clients\client-sysprin-writer\target\classes\rapid\sysprin\web\SysPrinController.class]: Unsatisfied dependency expressed through constructor parameter 0: Error creating bean with name 'sysPrinService' defined in URL [jar:file:/C:/Users/F2LIPBX/.m2/repository/trace-client-sysprin-service/common-services/0.0.1-SNAPSHOT/common-services-0.0.1-SNAPSHOT.jar!/rapid/service/sysprin/SysPrinService.class]: Unsatisfied dependency expressed through constructor parameter 7: Error creating bean with name 'sysPrinDetailDaoWithNativeSql': Injection of autowired dependencies failed
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
        at org.springframework.beans.factory.support.DefaultListableBeanFactory.instantiateSingleton(DefaultListableBeanFactory.java:1222) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.DefaultListableBeanFactory.preInstantiateSingleton(DefaultListableBeanFactory.java:1188) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.DefaultListableBeanFactory.preInstantiateSingletons(DefaultListableBeanFactory.java:1123) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.context.support.AbstractApplicationContext.finishBeanFactoryInitialization(AbstractApplicationContext.java:987) ~[spring-context-6.2.7.jar:6.2.7]
        at org.springframework.context.support.AbstractApplicationContext.refresh(AbstractApplicationContext.java:627) ~[spring-context-6.2.7.jar:6.2.7]
        at org.springframework.boot.web.servlet.context.ServletWebServerApplicationContext.refresh(ServletWebServerApplicationContext.java:146) ~[spring-boot-3.5.0.jar:3.5.0]
        at org.springframework.boot.SpringApplication.refresh(SpringApplication.java:753) ~[spring-boot-3.5.0.jar:3.5.0]
        at org.springframework.boot.SpringApplication.refreshContext(SpringApplication.java:439) ~[spring-boot-3.5.0.jar:3.5.0]
        at org.springframework.boot.SpringApplication.run(SpringApplication.java:318) ~[spring-boot-3.5.0.jar:3.5.0]
        at rapid.writer.ClientSysPrinWriterApplication.main(ClientSysPrinWriterApplication.java:28) ~[classes/:na]
Caused by: org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating bean with name 'sysPrinService' defined in URL [jar:file:/C:/Users/F2LIPBX/.m2/repository/trace-client-sysprin-service/common-services/0.0.1-SNAPSHOT/common-services-0.0.1-SNAPSHOT.jar!/rapid/service/sysprin/SysPrinService.class]: Unsatisfied dependency expressed through constructor parameter 7: Error creating bean with name 'sysPrinDetailDaoWithNativeSql': Injection of autowired dependencies failed
        at org.springframework.beans.factory.support.ConstructorResolver.createArgumentArray(ConstructorResolver.java:804) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.ConstructorResolver.autowireConstructor(ConstructorResolver.java:240) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.autowireConstructor(AbstractAutowireCapableBeanFactory.java:1395) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework
