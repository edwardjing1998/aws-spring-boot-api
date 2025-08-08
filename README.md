mvn clean deploy -DskipTests=true
[INFO] Scanning for projects...
[INFO] ------------------------------------------------------------------------
[INFO] Reactor Build Order:
[INFO] 
[INFO] trace-client-sysprin-service                                       [pom]
[INFO] common-model                                                       [jar]
[INFO] common-api-dto                                                     [jar]
[INFO] common-mapper                                                      [jar]
[INFO] client-sysprin-writer                                              [jar]
[INFO] client-sysprin-reader                                              [jar]
[INFO] gateway                                                            [jar]
[INFO] search-integration                                                 [jar]
[INFO] 
[INFO] -----< trace-client-sysprin-service:trace-client-sysprin-service >------
[INFO] Building trace-client-sysprin-service 0.0.2                        [1/8]
[INFO]   from pom.xml
[INFO] --------------------------------[ pom ]---------------------------------
[INFO] 
[INFO] --- clean:3.2.0:clean (default-clean) @ trace-client-sysprin-service ---
[INFO] 
[INFO] --- install:3.1.2:install (default-install) @ trace-client-sysprin-service ---
[INFO] Installing /newarch/apps/jenkins/sylp2nj1aue0003/workspace/_TRACE-Client-microservices-Java-2/pom.xml to /newarch/apps/maven-repo/trace-client-sysprin-service/trace-client-sysprin-service/0.0.2/trace-client-sysprin-service-0.0.2.pom
[INFO] 
[INFO] --- deploy:3.1.2:deploy (default-deploy) @ trace-client-sysprin-service ---
Uploading to nexus-ci-releases: https://nexus-ci.onefiserv.net/repository/mvn-na-issuer-distapp-coreservices-private-releases/trace-client-sysprin-service/trace-client-sysprin-service/0.0.2/trace-client-sysprin-service-0.0.2.pom
Progress (1): 4.2 kB
                    
[INFO] ------------------------------------------------------------------------
[INFO] Reactor Summary for trace-client-sysprin-service 0.0.2:
[INFO] 
[INFO] trace-client-sysprin-service ....................... FAILURE [  0.866 s]
[INFO] common-model ....................................... SKIPPED
[INFO] common-api-dto ..................................... SKIPPED
[INFO] common-mapper ...................................... SKIPPED
[INFO] client-sysprin-writer .............................. SKIPPED
[INFO] client-sysprin-reader .............................. SKIPPED
[INFO] gateway ............................................ SKIPPED
[INFO] search-integration ................................. SKIPPED
[INFO] ------------------------------------------------------------------------
[INFO] BUILD FAILURE
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  1.209 s
[INFO] Finished at: 2025-08-08T20:08:18Z
[INFO] ------------------------------------------------------------------------
[ERROR] Failed to execute goal org.apache.maven.plugins:maven-deploy-plugin:3.1.2:deploy (default-deploy) on project trace-client-sysprin-service: Failed to deploy artifacts: Could not transfer artifact trace-client-sysprin-service:trace-client-sysprin-service:pom:0.0.2 from/to nexus-ci-releases (https://nexus-ci.onefiserv.net/repository/mvn-na-issuer-distapp-coreservices-private-releases): status code: 400, reason phrase: mvn-na-issuer-distapp-coreservices-private-releases/trace-client-sysprin-service/trace-client-sysprin-service/0.0.2/trace-client-sysprin-service-0.0.2.pom cannot be updated (400) -> [Help 1]
[ERROR] 
[ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
[ERROR] Re-run Maven using the -X switch to enable full debug logging.
[ERROR] 
[ERROR] For more information about the errors and possible solutions, please read the following articles:
[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/MojoExecutionException
