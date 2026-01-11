[INFO]
[INFO] >>> spring-boot:3.5.0:run (default-cli) > test-compile @ trace-search-integration >>>
Downloading from nexus-ci-releases: https://nexus-ci.onefiserv.net/repository/mvn-na-issuer-distapp-coreservices-private-releases/trace-client-sysprin/trace-clients/$%7Brevision%7D/trace-clients-$%7Brevision%7D.pom
Downloading from Nexus: https://nexus-dev.onefiserv.net/repository/mvn-gl-flume-public-group/trace-client-sysprin/trace-clients/$%7Brevision%7D/trace-clients-$%7Brevision%7D.pom
[INFO] ------------------------------------------------------------------------
[INFO] BUILD FAILURE
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  20.999 s
[INFO] Finished at: 2026-01-10T19:03:56-06:00
[INFO] ------------------------------------------------------------------------
[ERROR] Failed to execute goal on project trace-search-integration: Could not resolve dependencies for project trace-client-sysprin:trace-search-integration:jar:1.0.0-SNAPSHOT: Failed to collect dependencies at trace-client-sysprin:trace-common-model:jar:1.0.0-SNAPSHOT: Failed to read artifact descriptor for trace-client-sysprin:trace-common-model:jar:1.0.0-SNAPSHOT: The following artifacts could not be resolved: trace-client-sysprin:trace-clients:pom:${revision} (absent): Could not transfer artifact trace-client-sysprin:trace-clients:pom:${revision} from/to Nexus (https://nexus-dev.onefiserv.net/repository/mvn-gl-flume-public-group/): status code: 400, reason phrase: Invalid repository path (400) -> [Help 1]
[ERROR]
[ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
[ERROR] Re-run Maven using the -X switch to enable full debug logging.
