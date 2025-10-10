e [C:\Users\F2LIPBX\spring_boot\harish\trace-voltage\trace-voltage-gateway\target\classes\voltage\service\FiservProtectorService.class]: Unsatisfied dependency expressed through constructor parameter 0: No qualifying bean of type 'com.fiserv.voltage.FiservProtector' available: expected at least 1 bean which qualifies as autowire candidate. Dependency annotations: {}
2025-10-09T19:00:28.506-05:00  INFO 17152 --- [Voltage] [           main] j.LocalContainerEntityManagerFactoryBean : Closing JPA EntityManagerFactory for persistence unit 'default'
2025-10-09T19:00:28.522-05:00  INFO 17152 --- [Voltage] [           main] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Shutdown initiated...
2025-10-09T19:00:28.718-05:00  INFO 17152 --- [Voltage] [           main] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Shutdown completed.
2025-10-09T19:00:28.729-05:00  INFO 17152 --- [Voltage] [           main] o.apache.catalina.core.StandardService   : Stopping service [Tomcat]
2025-10-09T19:00:28.830-05:00  INFO 17152 --- [Voltage] [           main] .s.b.a.l.ConditionEvaluationReportLogger :

Error starting ApplicationContext. To display the condition evaluation report re-run your application with 'debug' enabled.
2025-10-09T19:00:28.971-05:00 ERROR 17152 --- [Voltage] [           main] o.s.b.d.LoggingFailureAnalysisReporter   :

***************************
APPLICATION FAILED TO START
***************************

Description:

Parameter 0 of constructor in voltage.service.FiservProtectorService required a bean of type 'com.fiserv.voltage.FiservProtector' that could not be found.


Action:

Consider defining a bean of type 'com.fiserv.voltage.FiservProtector' in your configuration.

[INFO] ------------------------------------------------------------------------
[INFO] BUILD FAILURE
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  01:58 min
[INFO] Finished at: 2025-10-09T19:00:29-05:00
[INFO] ----------------------------------------
