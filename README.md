Hibernate: insert into deleted_transactions (command_line,cycle,retry_count,system_type,type,CASE_NUMBER,date_time,trans_no) values (?,?,?,?,?,?,?,?)
Hibernate: insert into deleted_transactions (command_line,cycle,retry_count,system_type,type,CASE_NUMBER,date_time,trans_no) values (?,?,?,?,?,?,?,?)
2025-08-29T11:34:40.908-05:00  WARN 31892 --- [case-reader] [           main] ConfigServletWebServerApplicationContext : Exception encountered during context initialization - cancelling refresh attempt: org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating bean with name 'clientServiceIntegration' defined in file [C:\Users\F2LIPBX\spring_boot\kalpana\trace-cases\case-reader\target\classes\rapid\cases\service\integration\ClientServiceIntegration.class]: Unsatisfied dependency expressed through constructor parameter 0: No qualifying bean of type 'org.springframework.web.reactive.function.client.WebClient' available: expected at least 1 bean which qualifies as autowire candidate. Dependency annotations: {}
2025-08-29T11:34:40.911-05:00  INFO 31892 --- [case-reader] [           main] j.LocalContainerEntityManagerFactoryBean : Closing JPA EntityManagerFactory for persistence unit 'default'
2025-08-29T11:34:40.917-05:00  INFO 31892 --- [case-reader] [           main] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Shutdown initiated...
2025-08-29T11:34:40.924-05:00  INFO 31892 --- [case-reader] [           main] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Shutdown completed.
2025-08-29T11:34:40.930-05:00  INFO 31892 --- [case-reader] [           main] o.apache.catalina.core.StandardService   : Stopping service [Tomcat]
2025-08-29T11:34:40.988-05:00  INFO 31892 --- [case-reader] [           main] .s.b.a.l.ConditionEvaluationReportLogger :

Error starting ApplicationContext. To display the condition evaluation report re-run your application with 'debug' enabled.
2025-08-29T11:34:41.048-05:00 ERROR 31892 --- [case-reader] [           main] o.s.b.d.LoggingFailureAnalysisReporter   :

***************************
APPLICATION FAILED TO START
***************************

Description:

Parameter 0 of constructor in rapid.cases.service.integration.ClientServiceIntegration required a bean of type 'org.springframework.web.reactive.function.client.WebClient' that could not be found.


Action:
