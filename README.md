[INFO] trace-common-mapper ................................ SUCCESS [  6.665 s]
[INFO] trace-voltage-gateway .............................. FAILURE [01:12 min]
[INFO] ------------------------------------------------------------------------
[INFO] BUILD FAILURE
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  01:53 min
[INFO] Finished at: 2025-10-09T18:19:01-05:00
[INFO] ------------------------------------------------------------------------
[ERROR] Failed to execute goal on project trace-voltage-gateway: Could not resolve dependencies for project trace-voltage-hub:trace-voltage-gateway:jar:1.0.0-SNAPSHOT: The following artifacts could not be resolved: trace-voltage-hub:trace-voltage-events:jar:1.0.0-SNAPSHOT (absent): Could not find artifact trace-voltage-hub:trace-voltage-events:jar:1.0.0-SNAPSHOT in nexus-ci-snapshots (https://nexus-ci.onefiserv.net/repository/mvn-na-issuer-distapp-coreservices-private-snapshots) -> [Help 1]                                    
[ERROR] 
[ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
[ERROR] Re-run Maven using the -X switch to enable full debug logging.
[ERROR] 
[ERROR] For more information about the errors and possible solutions, please read the following articles:
[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/DependencyResolutionException
[ERROR] 
[ERROR] After correcting the problems, you can resume the build with the command
[ERROR]   mvn <args> -rf :trace-voltage-gateway
