org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating bean with name 'caseDataGenerator' defined in file [C:\Users\F2LIPBX\spring_boot\kalpana\trace-cases\case-reader\target\classes\rapid\cases\config\data\CaseDataGenerator.class]: Unsatisfied dependency expressed through constructor parameter 0: Error creating bean with name 'caseRepo' defined in rapid.repository.cases.cases.CaseRepo defined in @EnableJpaRepositories declared on CaseReaderApplication: Could not create query for public abstract java.util.Optional rapid.repository.cases.cases.CaseRepo.findByPiIdAndActive(java.lang.String,int); Reason: Failed to create query for method public abstract java.util.Optional rapid.repository.cases.cases.CaseRepo.findByPiIdAndActive(java.lang.String,int); Cannot compare left expression of type 'java.lang.Boolean' with right expression of type 'java.lang.Integer'
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



        package rapid.repository.cases.cases;

import org.springframework.data.jpa.repository.Modifying;
import org.springframework.data.jpa.repository.Query;
import org.springframework.data.repository.query.Param;
import org.springframework.stereotype.Repository;
import rapid.model.cases.cases.Case;
import rapid.repository.cases.base.BaseCaseNumberRepo;

import java.util.List;
import java.util.Optional;

@Repository
public interface CaseRepo extends BaseCaseNumberRepo<Case, String> {
    List<Case> findByAccount(String account);

    @Modifying
    @Query("DELETE FROM Case cs WHERE cs.caseNumber IN :caseNumbers")
    void deleteAllByCaseNumberIn(@Param("caseNumbers") List<String> caseNumbers);

    Optional<Case> findByCaseNumber(String caseNumber);
    @Query("SELECT count(c) FROM Case c WHERE c.piId = :piId AND c.active = 1")
    long countActiveCasesByPiId(@Param("piId") String piId);
    
    Optional<Case> findByPiIdAndActive(String piId, int active);
    List<Case> findByPiIdOrderByCaseNumberDesc(String piId);
}
