2025-10-09T18:38:40.718-05:00  INFO 13632 --- [Voltage] [           main] o.h.e.t.j.p.i.JtaPlatformInitiator       : HHH000489: No JTA platform available (set 'hibernate.transaction.jta.platform' to enable JTA platform integration)
2025-10-09T18:38:40.752-05:00  INFO 13632 --- [Voltage] [           main] j.LocalContainerEntityManagerFactoryBean : Initialized JPA EntityManagerFactory for persistence unit 'default'
2025-10-09T18:38:40.792-05:00  WARN 13632 --- [Voltage] [           main] ConfigServletWebServerApplicationContext : Exception encountered during context initialization - cancelling refresh attempt: org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating bean with name 'fiservProtectorService' defined in file [C:\Users\F2LIPBX\spring_boot\harish\trace-voltage\trace-voltage-gateway\target\classes\voltage\service\FiservProtectorService.class]: Unsatisfied dependency expressed through constructor parameter 0: No qualifying bean of type 'com.fiserv.voltage.FiservProtector' available: expected at least 1 bean which qualifies as autowire candidate. Dependency annotations: {@org.springframework.beans.factory.annotation.Autowired(required=true)}
2025-10-09T18:38:40.794-05:00  INFO 13632 --- [Voltage] [           main] j.LocalContainerEntityManagerFactoryBean : Closing JPA EntityManagerFactory for persistence unit 'default'
2025-10-09T18:38:40.810-05:00  INFO 13632 --- [Voltage] [           main] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Shutdown initiated...
2025-10-09T18:38:41.617-05:00  INFO 13632 --- [Voltage] [           main] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Shutdown completed.
2025-10-09T18:38:41.627-05:00  INFO 13632 --- [Voltage] [           main] o.apache.catalina.core.StandardService   : Stopping service [Tomcat]
2025-10-09T18:38:41.717-05:00  INFO 13632 --- [Voltage] [           main] .s.b.a.l.ConditionEvaluationReportLogger :

Error starting ApplicationContext. To display the condition evaluation report re-run your application with 'debug' enabled.
2025-10-09T18:38:41.843-05:00 ERROR 13632 --- [Voltage] [           main] o.s.b.d.LoggingFailureAnalysisReporter   :

***************************
APPLICATION FAILED TO START
***************************

Description:

Parameter 0 of constructor in voltage.service.FiservProtectorService required a bean of type 'com.fiserv.voltage.FiservProtector' that could not be found.

The injection point has the following annotations:
        - @org.springframework.beans.factory.annotation.Autowired(required=true)


Action:

Consider defining a bean of type 'com.fiserv.voltage.FiservProtector' in your configuration.

[INFO] ------------------------------------------------------------------------
[INFO] BUILD FAILURE
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  01:04 min
[INFO] Finished at: 2025-10-09T18:38:42-05:00
[INFO] ------------------------------------------------------------------------
[ERROR] Failed to execute goal org.springframework.boot:spring-boot-maven-plugin:3.4.2:run (default-cli) on project trace-voltage-gateway: Process terminated with exit code: 1 -> [Help 1]
[ERROR]
[ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
[ERROR] Re-run Maven using the -X switch to enable full debug logging.
[ERROR]
[ERROR] For more information about the errors and possible solutions, please read the following articles:
[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/MojoExecutionException
