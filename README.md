[INFO] <<< spring-boot:3.2.12:run (default-cli) < test-compile @ dda-billing-manager <<<
[INFO]
[INFO]
[INFO] --- spring-boot:3.2.12:run (default-cli) @ dda-billing-manager ---
[INFO] Attaching agents: []
Logging system failed to initialize using configuration from 'null'
java.lang.NoSuchMethodError: 'java.lang.Object ch.qos.logback.classic.LoggerContext.getConfigurationLock()'
        at org.springframework.boot.logging.logback.LogbackConfigurator.getConfigurationLock(LogbackConfigurator.java:53)
        at org.springframework.boot.logging.logback.DefaultLogbackConfiguration.apply(DefaultLogbackConfiguration.java:57)
        at org.springframework.boot.logging.logback.LogbackLoggingSystem.lambda$loadDefaults$0(LogbackLoggingSystem.java:234)
        at org.springframework.boot.logging.logback.LogbackLoggingSystem.withLoggingSuppressed(LogbackLoggingSystem.java:462)
        at org.springframework.boot.logging.logback.LogbackLoggingSystem.loadDefaults(LogbackLoggingSystem.java:223)
        at org.springframework.boot.logging.AbstractLoggingSystem.initializeWithConventions(AbstractLoggingSystem.java:84)
        at org.springframework.boot.logging.AbstractLoggingSystem.initialize(AbstractLoggingSystem.java:61)
        at org.springframework.boot.logging.logback.LogbackLoggingSystem.initialize(LogbackLoggingSystem.java:189)
        at org.springframework.boot.context.logging.LoggingApplicationListener.initializeSystem(LoggingApplicationListener.java:332)
        at org.springframework.boot.context.logging.LoggingApplicationListener.initialize(LoggingApplicationListener.java:298)
