SELECT 
    COLUMN_NAME, 
    COLUMNPROPERTY(OBJECT_ID(TABLE_SCHEMA + '.' + TABLE_NAME), COLUMN_NAME, 'IsIdentity') AS IsIdentity
FROM 
    INFORMATION_SCHEMA.COLUMNS
WHERE 
    TABLE_NAME = 'ADMIN_QUERY_LIST' AND COLUMN_NAME = 'report_id';


    @GeneratedValue(strategy = GenerationType.IDENTITY)




    t_db_userid,aql1_0.rerun_client_id,aql1_0.rerun_date_range_end,aql1_0.rerun_date_range_start,aql1_0.tab_delimited_flag from admin_query_list aql1_0 where aql1_0.report_id=?        
2025-05-10T11:19:24.230-05:00  WARN 17376 --- [           main] ConfigServletWebServerApplicationContext : Exception encountered during context initialization - cancelling refresh attempt: org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'dataGenerationOrchestrator': Invocation of init method failed
2025-05-10T11:19:24.232-05:00  INFO 17376 --- [           main] j.LocalContainerEntityManagerFactoryBean : Closing JPA EntityManagerFactory for persistence unit 'default'
2025-05-10T11:19:24.235-05:00  INFO 17376 --- [           main] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Shutdown initiated...
2025-05-10T11:19:24.240-05:00  INFO 17376 --- [           main] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Shutdown completed.
2025-05-10T11:19:24.244-05:00  INFO 17376 --- [           main] o.apache.catalina.core.StandardService   : Stopping service [Tomcat]
2025-05-10T11:19:24.263-05:00  INFO 17376 --- [           main] .s.b.a.l.ConditionEvaluationReportLogger : 

Error starting ApplicationContext. To display the condition evaluation report re-run your application with 'debug' enabled.
2025-05-10T11:19:24.290-05:00 ERROR 17376 --- [           main] o.s.boot.SpringApplication               : Application run failed

org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'dataGenerationOrchestrator': Invocation of init method failed
        at org.springframework.beans.factory.annotation.InitDestroyAnnotationBeanPostProcessor.postProcessBeforeInitialization(InitDestroyAnnotationBeanPostProcessor.java:222) ~[spring-beans-6.2.3.jar:6.2.3]
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.applyBeanPostProcessorsBeforeInitialization(AbstractAutowireCapableBeanFactory.java:423) ~[spring-beans-6.2.3.jar:6.2.3]
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.initializeBean(AbstractAutowireCapableBeanFactory.java:1804) ~[spring-beans-6.2.3.jar:6.2.3]
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.doCreateBean(AbstractAutowireCapableBeanFactory.java:601) ~[spring-beans-6.2.3.jar:6.2.3]   
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBean(AbstractAutowireCapableBeanFactory.java:523) ~[spring-beans-6.2.3.jar:6.2.3]     
        at org.springframework.beans.factory.support.AbstractBeanFactory.lambda$doGetBean$0(AbstractBeanFactory.java:339) ~[spring-beans-6.2.3.jar:6.2.3]
        at org.springframework.beans.factory.support.DefaultSingletonBeanRegistry.getSingleton(DefaultSingletonBeanRegistry.java:346) ~[spring-beans-6.2.3.jar:6.2.3]
        at org.springframework.beans.factory.support.AbstractBeanFactory.doGetBean(AbstractBeanFactory.java:337) ~[spring-beans-6.2.3.jar:6.2.3]
        at org.springframework.beans.factory.support.AbstractBeanFactory.getBean(AbstractBeanFactory.java:202) ~[spring-beans-6.2.3.jar:6.2.3]
        at org.springframework.beans.factory.support.DefaultListableBeanFactory.instantiateSingleton(DefaultListableBeanFactory.java:1155) ~[spring-beans-6.2.3.jar:6.2.3]
        at org.springframework.beans.factory.support.DefaultListableBeanFactory.preInstantiateSingleton(DefaultListableBeanFactory.java:1121) ~[spring-beans-6.2.3.jar:6.2.3]       
        at org.springframework.beans.factory.support.DefaultListableBeanFactory.preInstantiateSingletons(DefaultListableBeanFactory.java:1056) ~[spring-beans-6.2.3.jar:6.2.3]      
        at org.springframework.context.support.AbstractApplicationContext.finishBeanFactoryInitialization(AbstractApplicationContext.java:987) ~[spring-context-6.2.3.jar:6.2.3]    
        at org.springframework.context.support.AbstractApplicationContext.refresh(AbstractApplicationContext.java:627) ~[spring-context-6.2.3.jar:6.2.3]
        at org.springframework.boot.web.servlet.context.ServletWebServerApplicationContext.refresh(ServletWebServerApplicationContext.java:146) ~[spring-boot-3.4.3.jar:3.4.3]      
        at org.springframework.boot.SpringApplication.refresh(SpringApplication.java:752) ~[spring-boot-3.4.3.jar:3.4.3]
        at org.springframework.boot.SpringApplication.refreshContext(SpringApplication.java:439) ~[spring-boot-3.4.3.jar:3.4.3]
        at org.springframework.boot.SpringApplication.run(SpringApplication.java:318) ~[spring-boot-3.4.3.jar:3.4.3]
        at org.springframework.boot.SpringApplication.run(SpringApplication.java:1361) ~[spring-boot-3.4.3.jar:3.4.3]
        at org.springframework.boot.SpringApplication.run(SpringApplication.java:1350) ~[spring-boot-3.4.3.jar:3.4.3]
        at admin.RapidAdminApplication.main(RapidAdminApplication.java:12) ~[classes/:na]
Caused by: org.springframework.orm.ObjectOptimisticLockingFailureException: Row was updated or deleted by another transaction (or unsaved-value mapping was incorrect): [admin.model.AdminQueryList#1]
        at org.springframework.orm.jpa.vendor.HibernateJpaDialect.convertHibernateAccessException(HibernateJpaDialect.java:325) ~[spring-orm-6.2.3.jar:6.2.3]
        at org.springframework.orm.jpa.vendor.HibernateJpaDialect.translateExceptionIfPossible(HibernateJpaDialect.java:244) ~[spring-orm-6.2.3.jar:6.2.3]
        at org.springframework.orm.jpa.AbstractEntityManagerFactoryBean.translateExceptionIfPossible(AbstractEntityManagerFactoryBean.java:560) ~[spring-orm-6.2.3.jar:6.2.3]       
        at org.springframework.dao.support.ChainedPersistenceExceptionTranslator.translateExceptionIfPossible(ChainedPersistenceExceptionTranslator.java:61) ~[spring-tx-6.2.3.jar:6.2.3]
        at org.springframework.dao.support.DataAccessUtils.translateIfNecessary(DataAccessUtils.java:343) ~[spring-tx-6.2.3.jar:6.2.3]
        at org.springframework.dao.support.PersistenceExceptionTranslationInterceptor.invoke(PersistenceExceptionTranslationInterceptor.java:160) ~[spring-tx-6.2.3.jar:6.2.3]      
        at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:184) ~[spring-aop-6.2.3.jar:6.2.3]
        at org.springframework.data.jpa.repository.support.CrudMethodMetadataPostProcessor$CrudMethodMetadataPopulatingMethodInterceptor.invoke(CrudMethodMetadataPostProcessor.java:165) ~[spring-data-jpa-3.4.3.jar:3.4.3]
        at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:184) ~[spring-aop-6.2.3.jar:6.2.3]
        at org.springframework.aop.framework.JdkDynamicAopProxy.invoke(JdkDynamicAopProxy.java:223) ~[spring-aop-6.2.3.jar:6.2.3]
        at jdk.proxy2/jdk.proxy2.$Proxy151.saveAll(Unknown Source) ~[na:na]
        at admin.config.data.ClientDataGenerator.generateClients(ClientDataGenerator.java:83) ~[classes/:na]
        at admin.config.data.DataGenerationOrchestrator.runDataGeneration(DataGenerationOrchestrator.java:29) ~[classes/:na]
        at java.base/jdk.internal.reflect.DirectMethodHandleAccessor.invoke(DirectMethodHandleAccessor.java:104) ~[na:na]
        at java.base/java.lang.reflect.Method.invoke(Method.java:565) ~[na:na]
        at org.springframework.beans.factory.annotation.InitDestroyAnnotationBeanPostProcessor$LifecycleMethod.invoke(InitDestroyAnnotationBeanPostProcessor.java:457) ~[spring-beans-6.2.3.jar:6.2.3]
        at org.springframework.beans.factory.annotation.InitDestroyAnnotationBeanPostProcessor$LifecycleMetadata.invokeInitMethods(InitDestroyAnnotationBeanPostProcessor.java:401) ~[spring-beans-6.2.3.jar:6.2.3]
        at org.springframework.beans.factory.annotation.InitDestroyAnnotationBeanPostProcessor.postProcessBeforeInitialization(InitDestroyAnnotationBeanPostProcessor.java:219) ~[spring-beans-6.2.3.jar:6.2.3]
        ... 20 common frames omitted
Caused by: org.hibernate.StaleObjectStateException: Row was updated or deleted by another transaction (or unsaved-value mapping was incorrect): [admin.model.AdminQueryList#1]      
        at org.hibernate.event.internal.DefaultMergeEventListener.entityIsDetached(DefaultMergeEventListener.java:426) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]
        at org.hibernate.event.internal.DefaultMergeEventListener.merge(DefaultMergeEventListener.java:214) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]
        at org.hibernate.event.internal.DefaultMergeEventListener.doMerge(DefaultMergeEventListener.java:152) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]
        at org.hibernate.event.internal.DefaultMergeEventListener.onMerge(DefaultMergeEventListener.java:136) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]
        at org.hibernate.event.internal.DefaultMergeEventListener.onMerge(DefaultMergeEventListener.java:89) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]
        at org.hibernate.event.service.internal.EventListenerGroupImpl.fireEventOnEachListener(EventListenerGroupImpl.java:127) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]       
        at org.hibernate.internal.SessionImpl.fireMerge(SessionImpl.java:854) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]
        at org.hibernate.internal.SessionImpl.merge(SessionImpl.java:840) ~[hibernate-core-6.6.8.Final.jar:6.6.8.Final]
        at java.base/jdk.internal.reflect.DirectMethodHandleAccessor.invoke(DirectMethodHandleAccessor.java:104) ~[na:na]
        at java.base/java.lang.reflect.Method.invoke(Method.java:565) ~[na:na]
        at org.springframework.orm.jpa.SharedEntityManagerCreator$SharedEntityManagerInvocationHandler.invoke(SharedEntityManagerCreator.java:320) ~[spring-orm-6.2.3.jar:6.2.3]    
        at jdk.proxy2/jdk.proxy2.$Proxy138.merge(Unknown Source) ~[na:na]
        at org.springframework.data.jpa.repository.support.SimpleJpaRepository.save(SimpleJpaRepository.java:639) ~[spring-data-jpa-3.4.3.jar:3.4.3]
        at org.springframework.data.jpa.repository.support.SimpleJpaRepository.saveAll(SimpleJpaRepository.java:662) ~[spring-data-jpa-3.4.3.jar:3.4.3]
        at java.base/jdk.internal.reflect.DirectMethodHandleAccessor.invoke(DirectMethodHandleAccessor.java:104) ~[na:na]
        at java.base/java.lang.reflect.Method.invoke(Method.java:565) ~[na:na]
        at org.springframework.aop.support.AopUtils.invokeJoinpointUsingReflection(AopUtils.java:359) ~[spring-aop-6.2.3.jar:6.2.3]
        at org.springframework.data.repository.core.support.RepositoryMethodInvoker$RepositoryFragmentMethodInvoker.lambda$new$0(RepositoryMethodInvoker.java:277) ~[spring-data-commons-3.4.3.jar:3.4.3]
        at org.springframework.data.repository.core.support.RepositoryMethodInvoker.doInvoke(RepositoryMethodInvoker.java:170) ~[spring-data-commons-3.4.3.jar:3.4.3]
        at org.springframework.data.repository.core.support.RepositoryMethodInvoker.invoke(RepositoryMethodInvoker.java:158) ~[spring-data-commons-3.4.3.jar:3.4.3]
        at org.springframework.data.repository.core.support.RepositoryComposition$RepositoryFragments.invoke(RepositoryComposition.java:515) ~[spring-data-commons-3.4.3.jar:3.4.3] 
        at org.springframework.data.repository.core.support.RepositoryComposition.invoke(RepositoryComposition.java:284) ~[spring-data-commons-3.4.3.jar:3.4.3]
        at org.springframework.data.repository.core.support.RepositoryFactorySupport$ImplementationMethodExecutionInterceptor.invoke(RepositoryFactorySupport.java:752) ~[spring-data-commons-3.4.3.jar:3.4.3]
        at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:184) ~[spring-aop-6.2.3.jar:6.2.3]
        at org.springframework.data.repository.core.support.QueryExecutorMethodInterceptor.doInvoke(QueryExecutorMethodInterceptor.java:174) ~[spring-data-commons-3.4.3.jar:3.4.3] 
        at org.springframework.data.repository.core.support.QueryExecutorMethodInterceptor.invoke(QueryExecutorMethodInterceptor.java:149) ~[spring-data-commons-3.4.3.jar:3.4.3]   
        at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:184) ~[spring-aop-6.2.3.jar:6.2.3]
        at org.springframework.data.projection.DefaultMethodInvokingMethodInterceptor.invoke(DefaultMethodInvokingMethodInterceptor.java:69) ~[spring-data-commons-3.4.3.jar:3.4.3] 
        at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:184) ~[spring-aop-6.2.3.jar:6.2.3]
        at org.springframework.transaction.interceptor.TransactionAspectSupport.invokeWithinTransaction(TransactionAspectSupport.java:380) ~[spring-tx-6.2.3.jar:6.2.3]
        at org.springframework.transaction.interceptor.TransactionInterceptor.invoke(TransactionInterceptor.java:119) ~[spring-tx-6.2.3.jar:6.2.3]
        at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:184) ~[spring-aop-6.2.3.jar:6.2.3]
        at org.springframework.dao.support.PersistenceExceptionTranslationInterceptor.invoke(PersistenceExceptionTranslationInterceptor.java:138) ~[spring-tx-6.2.3.jar:6.2.3]      
        ... 32 common frames omitted

[INFO] ------------------------------------------------------------------------
[INFO] BUILD FAILURE
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  16.220 s
[INFO] Finished at: 2025-05-10T11:19:24-05:00
[INFO] ------------------------------------------------------------------------
[ERROR] Failed to execute goal org.springframework.boot:spring-boot-maven-plugin:3.4.3:run (default-cli) on project admin: Process terminated with exit code: 1 -> [Help 1]
[ERROR]
[ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
[ERROR] Re-run Maven using the -X switch to enable full debug logging.
[ERROR]
[ERROR] For more information about the errors and possible solutions, please read the following articles:
[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/MojoExecutionException
PS C:\Users\F2LIPBX\spring_boot\2025-04-12\RAPIDadmin-microservices-java> 


package admin.config.data;

import admin.model.*;
import admin.repository.AdminQueryListRepository;
import admin.repository.ClientRepository;
import admin.repository.InvalidDelivAreaRepository;
import org.springframework.context.annotation.Profile;
import org.springframework.stereotype.Component;

import java.util.*;

@Component
@Profile("local")
public class ClientDataGenerator {

    private final ClientRepository clientRepository;
    private final AdminQueryListRepository adminQueryListRepository;
    private final InvalidDelivAreaRepository invalidDelivAreaRepository;

    private final String[] usStates = {
            "AL", "AK", "AZ", "AR", "CA", "CO", "CT", "DE", "FL", "GA",
            "HI", "ID", "IL", "IN", "IA", "KS", "KY", "LA", "ME", "MD",
            "MA", "MI", "MN", "MS", "MO", "MT", "NE", "NV", "NH", "NJ",
            "NM", "NY", "NC", "ND", "OH", "OK", "OR", "PA", "RI", "SC",
            "SD", "TN", "TX", "UT", "VT", "VA", "WA", "WV", "WI", "WY"
    };

    private final String[] sampleCities = {
            "New York", "Los Angeles", "Chicago", "Houston", "Phoenix", "Philadelphia",
            "San Antonio", "San Diego", "Dallas", "San Jose", "Austin", "Jacksonville",
            "Fort Worth", "Columbus", "Charlotte", "San Francisco", "Indianapolis", "Seattle",
            "Denver", "Washington"
    };

    private final String[] usBanks = {
            "JPMorgan Chase", "Bank of America", "Wells Fargo", "Citigroup", "Goldman Sachs",
            "Morgan Stanley", "U.S. Bancorp", "PNC Financial Services", "Truist Financial", "Capital One Financial",
            "Charles Schwab", "Fifth Third Bancorp", "Ally Financial", "KeyCorp", "Regions Financial",
            "Huntington Bancshares", "First Republic Bank", "M&T Bank Corporation", "Citizens Financial", "Comerica",
            "Zions Bancorporation", "BOK Financial", "Synovus Financial", "Raymond James Financial", "SVB Financial",
            "First Horizon", "East West Bancorp", "New York Community Bancorp", "Wintrust Financial", "Western Alliance"
    };

    private final String[] queryNames = {
            "zipcode step 1 - T", "Phase 1 Retest", "ZipCode Step 2", "Phase3 test",
            "Rapid Daily Activity - AAID", "First Data Rapid Daily Activity Web Report HSBC",
            "Binary - Rapid Daily Activity Report 6056 - NDM", "ProductivityWork_Rpt",
            "Mailing SysPrin - T", "SPS - First Data Rapid Daily Activity Web Report",
            "First Data Rapid Activity Report", "SendToMIT", "Custom Excel Report",
            "Product Group Report", "Account Transfer Rejects Daily Report", "Amex Remail - 1"
    };

    private final Random random = new Random();

    public ClientDataGenerator(ClientRepository clientRepository,
                               AdminQueryListRepository adminQueryListRepository,
                               InvalidDelivAreaRepository invalidDelivAreaRepository) {
        this.clientRepository = clientRepository;
        this.adminQueryListRepository = adminQueryListRepository;
        this.invalidDelivAreaRepository = invalidDelivAreaRepository;
    }

    public void generateClients() {
        List<AdminQueryList> reports = new ArrayList<>();
        for (int j = 1; j <= 10; j++) {
            AdminQueryList report = new AdminQueryList();
            report.setReportId(j);
            report.setDefaultFileNm("Report " + j);
            report.setReportByClientFlag(random.nextBoolean());
            report.setDbDriverType("SQL Server");
            report.setQueryName(queryNames[random.nextInt(queryNames.length)]);
            report.setInputDataFields("Field1, Field2");
            report.setFileExt(random.nextBoolean() ? "TXT" : "XLT");
            report.setFileHeaderInd(1);
            report.setReportDbServer("localhost");
            report.setReportDb("demoDb");
            report.setReportDbUserid("admin");
            report.setReportDbPasswrd("adminpass");
            report.setReportDbIpAndPort("127.0.0.1:1433");
            reports.add(report);
        }

        adminQueryListRepository.saveAll(reports);
        adminQueryListRepository.flush();
        List<AdminQueryList> managedReports = adminQueryListRepository.findAll();

        List<Client> clients = new ArrayList<>();

        for (int i = 0; i < 20; i++) {
            String clientId = generateBase35Id(i);
            String billingSp = "B" + (i + 1);
            String city = sampleCities[random.nextInt(sampleCities.length)];
            String state = usStates[random.nextInt(usStates.length)];
            String zip = String.format("%05d", 10000 + random.nextInt(89999));
            String phone = "555-" + (1000000 + random.nextInt(8999999));
            String name = usBanks[random.nextInt(usBanks.length)];

            List<ClientReportOption> reportOptions = new ArrayList<>();
            for (int j = 1; j <= 10; j++) {
                ClientReportOption option = new ClientReportOption();
                option.setId(new ClientReportOptionId(clientId, j));
                option.setReceiveFlag(j % 2 == 0);
                option.setOutputTypeCd(random.nextInt(3));
                option.setFileTypeCd(random.nextInt(3));
                option.setEmailFlag(random.nextInt(3));
                option.setReportPasswordTx("pass" + j);
                option.setReport(managedReports.get((j - 1) % managedReports.size()));
                option.setEmailBodyTx("Email body for " + clientId + " item " + j);
                reportOptions.add(option);
            }

            Map<String, SysPrinsPrefix> prefixCache = new HashMap<>();
            List<SysPrinsPrefix> prefixes = new ArrayList<>();
            for (int k = 1; k <= 5 + random.nextInt(5); k++) {
                String atmCashRule = random.nextBoolean() ? "0" : "1"; // 1-char
                String prefixStr = "PR" + k; // shortened prefix name to avoid overflow
                String cacheKey = billingSp + "-" + atmCashRule + "-" + prefixStr;

                SysPrinsPrefix existingPrefix = prefixCache.get(cacheKey);
                if (existingPrefix == null) {
                    SysPrinsPrefixId prefixId = new SysPrinsPrefixId(billingSp, prefixStr, atmCashRule);
                    SysPrinsPrefix newPrefix = new SysPrinsPrefix();
                    newPrefix.setId(prefixId);
                    prefixCache.put(cacheKey, newPrefix);
                    prefixes.add(newPrefix);
                } else {
                    prefixes.add(existingPrefix);
                }
            }

            Client client = new Client(
                    clientId, name, "123 " + city + " St.", city, state, zip,
                    "Contact " + (i + 1), phone, (i + 1) % 2 == 0, "Fax-" + (i + 1), billingSp,
                    (i + 1) % 2, (i + 1) % 3, (i + 1) % 2 == 0, (i + 1) % 2 == 1, (i + 1) % 2,
                    "" + generateBase35Id(i + 1), (i + 1) % 2 == 0, reportOptions, prefixes, new ArrayList<>(), new ArrayList<>()
            );

            List<SysPrin> sysPrinsList = new ArrayList<>();
            int sysPrinCount = 2 + random.nextInt(4);
            for (int s = 1; s <= sysPrinCount; s++) {
                SysPrin sysPrin = new SysPrin();
                SysPrinId sysPrinId = new SysPrinId(clientId, "SP" + (s + 1));
                sysPrin.setId(sysPrinId);
                sysPrin.setCustType(String.valueOf(random.nextInt(3)));
                sysPrin.setUndeliverable(String.valueOf(1 + random.nextInt(4)));
                sysPrin.setStatA("1");
                sysPrin.setStatB("0");
                sysPrin.setStatC("1");
                sysPrin.setStatD("1");
                sysPrin.setStatE("0");
                sysPrin.setStatF("1");
                sysPrin.setStatI("1");
                sysPrin.setStatL("0");
                sysPrin.setStatO("1");
                sysPrin.setStatU("1");
                sysPrin.setStatX("0");
                sysPrin.setStatZ("1");
                sysPrin.setPoBox(String.valueOf(1 + random.nextInt(3)));
                sysPrin.setAddrFlag(random.nextBoolean() ? "Y" : "N");
                sysPrin.setTempAway(100L + s);
                sysPrin.setRps(random.nextBoolean() ? "Y" : "N");
                sysPrin.setSession("A");
                sysPrin.setBadState(String.valueOf(1 + random.nextInt(3)));
                sysPrin.setAStatRch(String.valueOf(random.nextInt(2)));
                sysPrin.setNm13(String.valueOf(random.nextInt(2)));
                sysPrin.setTempAwayAtts(200L + s);
                sysPrin.setReportMethod(0.0);
                sysPrin.setActive(true);
                sysPrin.setNotes("Note" + s);
                sysPrin.setReturnStatus("A");
                sysPrin.setDestroyStatus("0");
                sysPrin.setSpecial("1");
                sysPrin.setPinMailer("0");
                sysPrin.setHoldDays(10);
                sysPrin.setNonUS(String.valueOf(1 + random.nextInt(3)));
                sysPrin.setForwardingAddress("1");
                sysPrin.setContact("Contact");
                sysPrin.setPhone("Phone");
                sysPrin.setEntityCode("0");
                sysPrin.setClient(client);

                List<InvalidDelivArea> invalidAreas = new ArrayList<>();
                for (int a = 1; a <= 5; a++) {
                    String randomState = usStates[random.nextInt(usStates.length)];
                    InvalidDelivArea area = new InvalidDelivArea(new InvalidDelivAreaId(sysPrin.getId().getSysPrin(), randomState));
                    invalidAreas.add(area);
                }
                invalidDelivAreaRepository.saveAll(invalidAreas);

                sysPrinsList.add(sysPrin);
            }
            client.setSysPrins(sysPrinsList);

            List<ClientEmail> emails = new ArrayList<>();
            for (int e = 1; e <= 3 + random.nextInt(3); e++) {
                ClientEmail email = new ClientEmail();
                ClientEmailId emailId = new ClientEmailId(clientId, "user" + e + "@example.com");
                email.setId(emailId);
                email.setReportId((long) (1 + random.nextInt(5)));
                email.setEmailNameTx("User" + e);
                email.setCarbonCopyFlag(e % 2 == 0);
                email.setActiveFlag(random.nextBoolean());
                email.setMailServerId((long) random.nextInt(2));
                email.setClient(client);
                emails.add(email);
            }
            client.setClientEmails(emails);

            clients.add(client);
        }

        clientRepository.saveAll(clients);
    }


    private String generateBase35Id(int index) {
        char[] base35Chars = "123456789abcdefghijklmnopqrstuvwxyz".toCharArray();
        StringBuilder sb = new StringBuilder();
        int value = index;
        do {
            sb.insert(0, base35Chars[value % base35Chars.length]);
            value /= base35Chars.length;
        } while (value > 0);
        while (sb.length() < 4) sb.insert(0, '1'); // pad to 4 characters
        return sb.toString();
    }
}

if (!adminQueryListRepository.existsById(j)) {









