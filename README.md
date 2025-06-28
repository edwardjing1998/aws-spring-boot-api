Downloaded from Nexus: https://nexus-dev.onefiserv.net/repository/mvn-gl-flume-public-group/io/fabric8/kubernetes-client-bom/7.3.1/kubernetes-client-bom-7.3.1.pom (21 kB at 62 kB/s)
Error: ] Some problems were encountered while processing the POMs:
Warning:  'parent.relativePath' of POM case.service:service-c:0.0.1-SNAPSHOT (/home/runner/_work/RAPIDadmin-microservices-java/RAPIDadmin-microservices-java/service-c/pom.xml) points at rapid-client-case-hub:rapid-client-case-hub instead of case.service:rapid-client-case-hub, please verify your project structure @ line 6, column 13
[FATAL] Non-resolvable parent POM for case.service:service-c:0.0.1-SNAPSHOT: The following artifacts could not be resolved: case.service:rapid-client-case-hub:pom:0.0.1-SNAPSHOT (absent): Could not find artifact case.service:rapid-client-case-hub:pom:0.0.1-SNAPSHOT in Nexus (https://nexus-dev.onefiserv.net/repository/mvn-gl-flume-public-group/) and 'parent.relativePath' points at wrong local POM @ line 6, column 13
 @ 
Error:  The build could not read 1 project -> [Help 1]
Error:    
Error:    The project case.service:service-c:0.0.1-SNAPSHOT (/home/runner/_work/RAPIDadmin-microservices-java/RAPIDadmin-microservices-java/service-c/pom.xml) has 1 error
Error:      Non-resolvable parent POM for case.service:service-c:0.0.1-SNAPSHOT: The following artifacts could not be resolved: case.service:rapid-client-case-hub:pom:0.0.1-SNAPSHOT (absent): Could not find artifact case.service:rapid-client-case-hub:pom:0.0.1-SNAPSHOT in Nexus (https://nexus-dev.onefiserv.net/repository/mvn-gl-flume-public-group/) and 'parent.relativePath' points at wrong local POM @ line 6, column 13 -> [Help 2]
Error:  
Error:  To see the full stack trace of the errors, re-run Maven with the -e switch.
Error:  Re-run Maven using the -X switch to enable full debug logging.
Error:  
Error:  For more information about the errors and possible solutions, please read the following articles:
Error:  [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/ProjectBuildingException
Error:  [Help 2] http://cwiki.apache.org/confluence/display/MAVEN/UnresolvableModelException
Error: Process completed with exit code 1.
