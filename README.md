+ mvn clean deploy -DskipTests=true
[INFO] Scanning for projects...
[INFO] ------------------------------------------------------------------------
[INFO] Reactor Build Order:
[INFO] 
[INFO] trace-client-sysprin-service                                       [pom]
[INFO] common-model                                                       [jar]
[INFO] common-api-dto                                                     [jar]
[INFO] common-mapper                                                      [jar]
[INFO] 
[INFO] -----< trace-client-sysprin-service:trace-client-sysprin-service >------
[INFO] Building trace-client-sysprin-service 1.0.6                        [1/4]
[INFO]   from pom.xml
[INFO] --------------------------------[ pom ]---------------------------------
[INFO] 
[INFO] --- clean:3.2.0:clean (default-clean) @ trace-client-sysprin-service ---
[INFO] 
[INFO] --- install:3.1.2:install (default-install) @ trace-client-sysprin-service ---
[INFO] Installing /newarch/apps/jenkins/sylp2nj1aue0005/workspace/ava_release_TRACE-Common-Library-2/pom.xml to /newarch/apps/maven-repo/trace-client-sysprin-service/trace-client-sysprin-service/1.0.6/trace-client-sysprin-service-1.0.6.pom
[INFO] 
[INFO] --- deploy:3.1.2:deploy (default-deploy) @ trace-client-sysprin-service ---
Uploading to nexus-ci-releases: https://nexus-ci.onefiserv.net/repository/mvn-na-issuer-distapp-coreservices-private-releases/trace-client-sysprin-service/trace-client-sysprin-service/1.0.6/trace-client-sysprin-service-1.0.6.pom
Progress (1): 4.3 kB
                    
Uploaded to nexus-ci-releases: https://nexus-ci.onefiserv.net/repository/mvn-na-issuer-distapp-coreservices-private-releases/trace-client-sysprin-service/trace-client-sysprin-service/1.0.6/trace-client-sysprin-service-1.0.6.pom (4.3 kB at 2.8 kB/s)
Downloading from nexus-ci-releases: https://nexus-ci.onefiserv.net/repository/mvn-na-issuer-distapp-coreservices-private-releases/trace-client-sysprin-service/trace-client-sysprin-service/maven-metadata.xml
Progress (1): 522 B
                   
Downloaded from nexus-ci-releases: https://nexus-ci.onefiserv.net/repository/mvn-na-issuer-distapp-coreservices-private-releases/trace-client-sysprin-service/trace-client-sysprin-service/maven-metadata.xml (522 B at 3.8 kB/s)
Uploading to nexus-ci-releases: https://nexus-ci.onefiserv.net/repository/mvn-na-issuer-distapp-coreservices-private-releases/trace-client-sysprin-service/trace-client-sysprin-service/maven-metadata.xml
Progress (1): 553 B
                   
Uploaded to nexus-ci-releases: https://nexus-ci.onefiserv.net/repository/mvn-na-issuer-distapp-coreservices-private-releases/trace-client-sysprin-service/trace-client-sysprin-service/maven-metadata.xml (553 B at 932 B/s)
[INFO] 
[INFO] -------------< trace-client-sysprin-service:common-model >--------------
[INFO] Building common-model 1.0.6                                        [2/4]
[INFO]   from common-model/pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- clean:3.2.0:clean (default-clean) @ common-model ---
[INFO] Deleting /newarch/apps/jenkins/sylp2nj1aue0005/workspace/ava_release_TRACE-Common-Library-2/common-model/target
[INFO] 
[INFO] --- resources:3.3.1:resources (default-resources) @ common-model ---
[WARNING] Using platform encoding (UTF-8 actually) to copy filtered resources, i.e. build is platform dependent!
[INFO] Copying 11 resources from src/main/resources to target/classes
[INFO] 
[INFO] --- compiler:3.13.0:compile (default-compile) @ common-model ---
[INFO] Recompiling the module because of changed source code.
[WARNING] File encoding has not been set, using platform encoding UTF-8, i.e. build is platform dependent!
[INFO] Compiling 39 source files with javac [debug parameters release 21] to target/classes
[WARNING] /newarch/apps/jenkins/sylp2nj1aue0005/workspace/ava_release_TRACE-Common-Library-2/common-model/src/main/java/rapid/model/client/C3FileTransfer.java:[9,1] Generating equals/hashCode implementation but without a call to superclass, even though this class does not extend java.lang.Object. If this is intentional, add '@EqualsAndHashCode(callSuper=false)' to your type.
[WARNING] /newarch/apps/jenkins/sylp2nj1aue0005/workspace/ava_release_TRACE-Common-Library-2/common-model/src/main/java/rapid/model/client/ClientEmail.java:[10,1] Generating equals/hashCode implementation but without a call to superclass, even though this class does not extend java.lang.Object. If this is intentional, add '@EqualsAndHashCode(callSuper=false)' to your type.
[WARNING] /newarch/apps/jenkins/sylp2nj1aue0005/workspace/ava_release_TRACE-Common-Library-2/common-model/src/main/java/rapid/model/sysprin/vendor/VendorReceivedFrom.java:[13,1] Generating equals/hashCode implementation but without a call to superclass, even though this class does not extend java.lang.Object. If this is intentional, add '@EqualsAndHashCode(callSuper=false)' to your type.
[WARNING] /newarch/apps/jenkins/sylp2nj1aue0005/workspace/ava_release_TRACE-Common-Library-2/common-model/src/main/java/rapid/model/client/ClientReportOption.java:[12,1] Generating equals/hashCode implementation but without a call to superclass, even though this class does not extend java.lang.Object. If this is intentional, add '@EqualsAndHashCode(callSuper=false)' to your type.
[WARNING] /newarch/apps/jenkins/sylp2nj1aue0005/workspace/ava_release_TRACE-Common-Library-2/common-model/src/main/java/rapid/model/client/AdminQueryList.java:[12,1] Generating equals/hashCode implementation but without a call to superclass, even though this class does not extend java.lang.Object. If this is intentional, add '@EqualsAndHashCode(callSuper=false)' to your type.
[WARNING] /newarch/apps/jenkins/sylp2nj1aue0005/workspace/ava_release_TRACE-Common-Library-2/common-model/src/main/java/rapid/model/client/Client.java:[14,1] Generating equals/hashCode implementation but without a call to superclass, even though this class does not extend java.lang.Object. If this is intentional, add '@EqualsAndHashCode(callSuper=false)' to your type.
[WARNING] /newarch/apps/jenkins/sylp2nj1aue0005/workspace/ava_release_TRACE-Common-Library-2/common-model/src/main/java/rapid/model/sysprin/vendor/VendorSentTo.java:[13,1] Generating equals/hashCode implementation but without a call to superclass, even though this class does not extend java.lang.Object. If this is intentional, add '@EqualsAndHashCode(callSuper=false)' to your type.
[INFO] /newarch/apps/jenkins/sylp2nj1aue0005/workspace/ava_release_TRACE-Common-Library-2/common-model/src/main/java/rapid/model/client/ClientReportOption.java: /newarch/apps/jenkins/sylp2nj1aue0005/workspace/ava_release_TRACE-Common-Library-2/common-model/src/main/java/rapid/model/client/ClientReportOption.java uses or overrides a deprecated API.
[INFO] /newarch/apps/jenkins/sylp2nj1aue0005/workspace/ava_release_TRACE-Common-Library-2/common-model/src/main/java/rapid/model/client/ClientReportOption.java: Recompile with -Xlint:deprecation for details.
[INFO] 
[INFO] --- resources:3.3.1:testResources (default-testResources) @ common-model ---
[WARNING] Using platform encoding (UTF-8 actually) to copy filtered resources, i.e. build is platform dependent!
[INFO] Copying 1 resource from src/test/resources to target/test-classes
[INFO] 
[INFO] --- compiler:3.13.0:testCompile (default-testCompile) @ common-model ---
[INFO] Recompiling the module because of changed dependency.
[WARNING] File encoding has not been set, using platform encoding UTF-8, i.e. build is platform dependent!
[INFO] Compiling 3 source files with javac [debug parameters release 21] to target/test-classes
[INFO] 
[INFO] --- surefire:3.2.5:test (default-test) @ common-model ---
[INFO] Tests are skipped.
[INFO] 
[INFO] --- jar:3.4.2:jar (default-jar) @ common-model ---
[INFO] Building jar: /newarch/apps/jenkins/sylp2nj1aue0005/workspace/ava_release_TRACE-Common-Library-2/common-model/target/common-model-1.0.6.jar
[INFO] 
[INFO] --- install:3.1.2:install (default-install) @ common-model ---
[INFO] Installing /newarch/apps/jenkins/sylp2nj1aue0005/workspace/ava_release_TRACE-Common-Library-2/common-model/pom.xml to /newarch/apps/maven-repo/trace-client-sysprin-service/common-model/1.0.6/common-model-1.0.6.pom
[INFO] Installing /newarch/apps/jenkins/sylp2nj1aue0005/workspace/ava_release_TRACE-Common-Library-2/common-model/target/common-model-1.0.6.jar to /newarch/apps/maven-repo/trace-client-sysprin-service/common-model/1.0.6/common-model-1.0.6.jar
[INFO] 
[INFO] --- deploy:3.1.2:deploy (default-deploy) @ common-model ---
Uploading to nexus-ci-releases: https://nexus-ci.onefiserv.net/repository/mvn-na-issuer-distapp-coreservices-private-releases/trace-client-sysprin-service/common-model/1.0.6/common-model-1.0.6.pom
Progress (1): 4.4 kB
                    
Uploaded to nexus-ci-releases: https://nexus-ci.onefiserv.net/repository/mvn-na-issuer-distapp-coreservices-private-releases/trace-client-sysprin-service/common-model/1.0.6/common-model-1.0.6.pom (4.4 kB at 5.4 kB/s)
Uploading to nexus-ci-releases: https://nexus-ci.onefiserv.net/repository/mvn-na-issuer-distapp-coreservices-private-releases/trace-client-sysprin-service/common-model/1.0.6/common-model-1.0.6.jar
Progress (1): 33/73 kB
Progress (1): 66/73 kB
Progress (1): 73 kB   
                   
Uploaded to nexus-ci-releases: https://nexus-ci.onefiserv.net/repository/mvn-na-issuer-distapp-coreservices-private-releases/trace-client-sysprin-service/common-model/1.0.6/common-model-1.0.6.jar (73 kB at 96 kB/s)
Downloading from nexus-ci-releases: https://nexus-ci.onefiserv.net/repository/mvn-na-issuer-distapp-coreservices-private-releases/trace-client-sysprin-service/common-model/maven-metadata.xml
Progress (1): 506 B
                   
Downloaded from nexus-ci-releases: https://nexus-ci.onefiserv.net/repository/mvn-na-issuer-distapp-coreservices-private-releases/trace-client-sysprin-service/common-model/maven-metadata.xml (506 B at 3.0 kB/s)
Uploading to nexus-ci-releases: https://nexus-ci.onefiserv.net/repository/mvn-na-issuer-distapp-coreservices-private-releases/trace-client-sysprin-service/common-model/maven-metadata.xml
Progress (1): 537 B
                   
Uploaded to nexus-ci-releases: https://nexus-ci.onefiserv.net/repository/mvn-na-issuer-distapp-coreservices-private-releases/trace-client-sysprin-service/common-model/maven-metadata.xml (537 B at 916 B/s)
[INFO] 
[INFO] ------------< trace-client-sysprin-service:common-api-dto >-------------
[INFO] Building common-api-dto 1.0.6                                      [3/4]
[INFO]   from common-api-dto/pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- clean:3.2.0:clean (default-clean) @ common-api-dto ---
[INFO] Deleting /newarch/apps/jenkins/sylp2nj1aue0005/workspace/ava_release_TRACE-Common-Library-2/common-api-dto/target
[INFO] 
[INFO] --- resources:3.3.1:resources (default-resources) @ common-api-dto ---
[WARNING] Using platform encoding (UTF-8 actually) to copy filtered resources, i.e. build is platform dependent!
[INFO] skip non existing resourceDirectory /newarch/apps/jenkins/sylp2nj1aue0005/workspace/ava_release_TRACE-Common-Library-2/common-api-dto/src/main/resources
[INFO] 
[INFO] --- compiler:3.13.0:compile (default-compile) @ common-api-dto ---
[INFO] Recompiling the module because of changed source code.
[WARNING] File encoding has not been set, using platform encoding UTF-8, i.e. build is platform dependent!
[INFO] Compiling 13 source files with javac [debug parameters release 21] to target/classes
[INFO] 
[INFO] --- resources:3.3.1:testResources (default-testResources) @ common-api-dto ---
[WARNING] Using platform encoding (UTF-8 actually) to copy filtered resources, i.e. build is platform dependent!
[INFO] skip non existing resourceDirectory /newarch/apps/jenkins/sylp2nj1aue0005/workspace/ava_release_TRACE-Common-Library-2/common-api-dto/src/test/resources
[INFO] 
[INFO] --- compiler:3.13.0:testCompile (default-testCompile) @ common-api-dto ---
[INFO] No sources to compile
[INFO] 
[INFO] --- surefire:3.2.5:test (default-test) @ common-api-dto ---
[INFO] Tests are skipped.
[INFO] 
[INFO] --- jar:3.4.2:jar (default-jar) @ common-api-dto ---
[INFO] Building jar: /newarch/apps/jenkins/sylp2nj1aue0005/workspace/ava_release_TRACE-Common-Library-2/common-api-dto/target/common-api-dto-1.0.6.jar
[INFO] 
[INFO] --- spring-boot:3.5.0:repackage (default) @ common-api-dto ---
[INFO] 
[INFO] --- install:3.1.2:install (default-install) @ common-api-dto ---
[INFO] Installing /newarch/apps/jenkins/sylp2nj1aue0005/workspace/ava_release_TRACE-Common-Library-2/common-api-dto/pom.xml to /newarch/apps/maven-repo/trace-client-sysprin-service/common-api-dto/1.0.6/common-api-dto-1.0.6.pom
[INFO] Installing /newarch/apps/jenkins/sylp2nj1aue0005/workspace/ava_release_TRACE-Common-Library-2/common-api-dto/target/common-api-dto-1.0.6.jar to /newarch/apps/maven-repo/trace-client-sysprin-service/common-api-dto/1.0.6/common-api-dto-1.0.6.jar
[INFO] 
[INFO] --- deploy:3.1.2:deploy (default-deploy) @ common-api-dto ---
Uploading to nexus-ci-releases: https://nexus-ci.onefiserv.net/repository/mvn-na-issuer-distapp-coreservices-private-releases/trace-client-sysprin-service/common-api-dto/1.0.6/common-api-dto-1.0.6.pom
Progress (1): 3.7 kB
                    
Uploaded to nexus-ci-releases: https://nexus-ci.onefiserv.net/repository/mvn-na-issuer-distapp-coreservices-private-releases/trace-client-sysprin-service/common-api-dto/1.0.6/common-api-dto-1.0.6.pom (3.7 kB at 5.4 kB/s)
Uploading to nexus-ci-releases: https://nexus-ci.onefiserv.net/repository/mvn-na-issuer-distapp-coreservices-private-releases/trace-client-sysprin-service/common-api-dto/1.0.6/common-api-dto-1.0.6.jar
Progress (1): 33/42 kB
Progress (1): 42 kB   
                   
Uploaded to nexus-ci-releases: https://nexus-ci.onefiserv.net/repository/mvn-na-issuer-distapp-coreservices-private-releases/trace-client-sysprin-service/common-api-dto/1.0.6/common-api-dto-1.0.6.jar (42 kB at 53 kB/s)
Downloading from nexus-ci-releases: https://nexus-ci.onefiserv.net/repository/mvn-na-issuer-distapp-coreservices-private-releases/trace-client-sysprin-service/common-api-dto/maven-metadata.xml
Progress (1): 508 B
                   
Downloaded from nexus-ci-releases: https://nexus-ci.onefiserv.net/repository/mvn-na-issuer-distapp-coreservices-private-releases/trace-client-sysprin-service/common-api-dto/maven-metadata.xml (508 B at 4.1 kB/s)
Uploading to nexus-ci-releases: https://nexus-ci.onefiserv.net/repository/mvn-na-issuer-distapp-coreservices-private-releases/trace-client-sysprin-service/common-api-dto/maven-metadata.xml
Progress (1): 539 B
                   
Uploaded to nexus-ci-releases: https://nexus-ci.onefiserv.net/repository/mvn-na-issuer-distapp-coreservices-private-releases/trace-client-sysprin-service/common-api-dto/maven-metadata.xml (539 B at 921 B/s)
[INFO] 
[INFO] -------------< trace-client-sysprin-service:common-mapper >-------------
[INFO] Building common-mapper 1.0.6                                       [4/4]
[INFO]   from common-mapper/pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- clean:3.2.0:clean (default-clean) @ common-mapper ---
[INFO] Deleting /newarch/apps/jenkins/sylp2nj1aue0005/workspace/ava_release_TRACE-Common-Library-2/common-mapper/target
[INFO] 
[INFO] --- resources:3.3.1:resources (default-resources) @ common-mapper ---
[WARNING] Using platform encoding (UTF-8 actually) to copy filtered resources, i.e. build is platform dependent!
[INFO] skip non existing resourceDirectory /newarch/apps/jenkins/sylp2nj1aue0005/workspace/ava_release_TRACE-Common-Library-2/common-mapper/src/main/resources
[INFO] 
[INFO] --- compiler:3.13.0:compile (default-compile) @ common-mapper ---
[INFO] Recompiling the module because of changed dependency.
[WARNING] File encoding has not been set, using platform encoding UTF-8, i.e. build is platform dependent!
[INFO] Compiling 16 source files with javac [debug parameters release 21] to target/classes
[WARNING] /newarch/apps/jenkins/sylp2nj1aue0005/workspace/ava_release_TRACE-Common-Library-2/common-mapper/src/main/java/rapid/client/mapper/ClientMapper.java:[52,12] Unmapped target property: "clientEmails".
[WARNING] /newarch/apps/jenkins/sylp2nj1aue0005/workspace/ava_release_TRACE-Common-Library-2/common-mapper/src/main/java/rapid/client/mapper/ClientMapper.java:[55,10] Unmapped target property: "clientEmails".
[WARNING] /newarch/apps/jenkins/sylp2nj1aue0005/workspace/ava_release_TRACE-Common-Library-2/common-mapper/src/main/java/rapid/sysPrin/mapper/VendorReceivedFromMapper.java:[22,27] Unmapped target properties: "id, name, receiver". Mapping from property "Vendor vendor" to "VendorDTO vendor".
[WARNING] /newarch/apps/jenkins/sylp2nj1aue0005/workspace/ava_release_TRACE-Common-Library-2/common-mapper/src/main/java/rapid/sysPrin/mapper/VendorReceivedFromMapper.java:[35,24] Unmapped target properties: "vendId, vendName, vendReceiverCode". Mapping from property "VendorDTO vendor" to "Vendor vendor".
[WARNING] /newarch/apps/jenkins/sylp2nj1aue0005/workspace/ava_release_TRACE-Common-Library-2/common-mapper/src/main/java/rapid/sysPrin/mapper/VendorSentToMapper.java:[22,21] Unmapped target properties: "id, name, receiver". Mapping from property "Vendor vendor" to "VendorDTO vendor".
[WARNING] /newarch/apps/jenkins/sylp2nj1aue0005/workspace/ava_release_TRACE-Common-Library-2/common-mapper/src/main/java/rapid/sysPrin/mapper/VendorSentToMapper.java:[35,18] Unmapped target properties: "vendId, vendName, vendReceiverCode". Mapping from property "VendorDTO vendor" to "Vendor vendor".
[WARNING] /newarch/apps/jenkins/sylp2nj1aue0005/workspace/ava_release_TRACE-Common-Library-2/common-mapper/src/main/java/rapid/client/mapper/C3FileTransferMapper.java:[39,23] Unmapped target property: "adminQueryListId".
[INFO] 
[INFO] --- resources:3.3.1:testResources (default-testResources) @ common-mapper ---
[WARNING] Using platform encoding (UTF-8 actually) to copy filtered resources, i.e. build is platform dependent!
[INFO] skip non existing resourceDirectory /newarch/apps/jenkins/sylp2nj1aue0005/workspace/ava_release_TRACE-Common-Library-2/common-mapper/src/test/resources
[INFO] 
[INFO] --- compiler:3.13.0:testCompile (default-testCompile) @ common-mapper ---
[INFO] No sources to compile
[INFO] 
[INFO] --- surefire:3.2.5:test (default-test) @ common-mapper ---
[INFO] Tests are skipped.
[INFO] 
[INFO] --- jar:3.4.2:jar (default-jar) @ common-mapper ---
[INFO] Building jar: /newarch/apps/jenkins/sylp2nj1aue0005/workspace/ava_release_TRACE-Common-Library-2/common-mapper/target/common-mapper-1.0.6.jar
[INFO] 
[INFO] --- install:3.1.2:install (default-install) @ common-mapper ---
[INFO] Installing /newarch/apps/jenkins/sylp2nj1aue0005/workspace/ava_release_TRACE-Common-Library-2/common-mapper/pom.xml to /newarch/apps/maven-repo/trace-client-sysprin-service/common-mapper/1.0.6/common-mapper-1.0.6.pom
[INFO] Installing /newarch/apps/jenkins/sylp2nj1aue0005/workspace/ava_release_TRACE-Common-Library-2/common-mapper/target/common-mapper-1.0.6.jar to /newarch/apps/maven-repo/trace-client-sysprin-service/common-mapper/1.0.6/common-mapper-1.0.6.jar
[INFO] 
[INFO] --- deploy:3.1.2:deploy (default-deploy) @ common-mapper ---
Uploading to nexus-ci-releases: https://nexus-ci.onefiserv.net/repository/mvn-na-issuer-distapp-coreservices-private-releases/trace-client-sysprin-service/common-mapper/1.0.6/common-mapper-1.0.6.pom
Progress (1): 5.0 kB
                    
Uploaded to nexus-ci-releases: https://nexus-ci.onefiserv.net/repository/mvn-na-issuer-distapp-coreservices-private-releases/trace-client-sysprin-service/common-mapper/1.0.6/common-mapper-1.0.6.pom (5.0 kB at 7.8 kB/s)
Uploading to nexus-ci-releases: https://nexus-ci.onefiserv.net/repository/mvn-na-issuer-distapp-coreservices-private-releases/trace-client-sysprin-service/common-mapper/1.0.6/common-mapper-1.0.6.jar
Progress (1): 33/41 kB
Progress (1): 41 kB   
                   
Uploaded to nexus-ci-releases: https://nexus-ci.onefiserv.net/repository/mvn-na-issuer-distapp-coreservices-private-releases/trace-client-sysprin-service/common-mapper/1.0.6/common-mapper-1.0.6.jar (41 kB at 59 kB/s)
Downloading from nexus-ci-releases: https://nexus-ci.onefiserv.net/repository/mvn-na-issuer-distapp-coreservices-private-releases/trace-client-sysprin-service/common-mapper/maven-metadata.xml
Progress (1): 507 B
                   
Downloaded from nexus-ci-releases: https://nexus-ci.onefiserv.net/repository/mvn-na-issuer-distapp-coreservices-private-releases/trace-client-sysprin-service/common-mapper/maven-metadata.xml (507 B at 4.1 kB/s)
Uploading to nexus-ci-releases: https://nexus-ci.onefiserv.net/repository/mvn-na-issuer-distapp-coreservices-private-releases/trace-client-sysprin-service/common-mapper/maven-metadata.xml
Progress (1): 538 B
                   
Uploaded to nexus-ci-releases: https://nexus-ci.onefiserv.net/repository/mvn-na-issuer-distapp-coreservices-private-releases/trace-client-sysprin-service/common-mapper/maven-metadata.xml (538 B at 945 B/s)
[INFO] ------------------------------------------------------------------------
[INFO] Reactor Summary for trace-client-sysprin-service 1.0.6:
[INFO] 
[INFO] trace-client-sysprin-service ....................... SUCCESS [  2.601 s]
[INFO] common-model ....................................... SUCCESS [  5.963 s]
[INFO] common-api-dto ..................................... SUCCESS [  3.151 s]
[INFO] common-mapper ...................................... SUCCESS [  3.499 s]
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  15.553 s
[INFO] Finished at: 2025-08-09T05:06:38Z





<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>trace-client-sysprin-service</groupId>
        <artifactId>trace-client-sysprin-service</artifactId>
        <version>1.0.0-SNAPSHOT</version>
    </parent>

    <artifactId>common-mapper</artifactId>
    <version>1.0.0-SNAPSHOT</version>
    <name>common-mapper</name>
    <description>common-mapper</description>

    <packaging>jar</packaging>

    <properties>
        <sonar.projectName>GFS-FOS-${project.artifactId}</sonar.projectName>
        <sonar.projectVersion>${project.version}</sonar.projectVersion>
        <sonar.java.binaries>target/classes</sonar.java.binaries>
        <sonar.tests>src/test</sonar.tests>
        <sonar.sources>src/main</sonar.sources>

        <sonatype.appId>APM0012115_gfs-fos-trace-microservices-release-v1</sonatype.appId>
        <fortify.sscApplicationName>APM0012115</fortify.sscApplicationName>
        <fortify.sscApplicationVersion>gfs-fos-trace-microservices-release-v1</fortify.sscApplicationVersion>

        <rundeck.job.option.name>Service</rundeck.job.option.name>
        <rundeck.job.option.value>${project.artifactId}</rundeck.job.option.value>
        <rundeck.url>https://rundeck.1dc.com</rundeck.url>
        <rundeck.token>AbWbrusvtE5DwpX9fWqnXtx9MHqYKs1G</rundeck.token>
        <rundeck.job>8ba40041-e885-4e6d-a69a-004dce3b48cf</rundeck.job>
        <rundeck.job.params>SERVICE_NAME=${project.artifactId},VERSION=${project.version},NAMESPACE=trace-dev-comm-app,CONFIG_NAME=trace-dev-comm-app-1,TYPE=springboot,CLUSTER=${cluster.url}</rundeck.job.params>
        <cluster.url>https://api.syosxfftsm0001.fiserv.one:6443/</cluster.url>
    </properties>

    <scm>
        <developerConnection>scm:git:git@gitlab.onefiserv.net:issuers/fos-modernization/plastic/rapid/RAPID-Client-microservices-Java.git</developerConnection>
        <tag>HEAD</tag>
    </scm>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>${lombok.version}</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>com.h2database</groupId>
            <artifactId>h2</artifactId>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>org.mapstruct</groupId>
            <artifactId>mapstruct</artifactId>
            <version>${mapstruct.version}</version>
        </dependency>
        <dependency>
            <groupId>trace-client-sysprin-service</groupId>
            <artifactId>common-model</artifactId>
            <version>1.0.6</version>
        </dependency>
        <dependency>
            <groupId>trace-client-sysprin-service</groupId>
            <artifactId>common-api-dto</artifactId>
            <version>1.0.6</version>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>${mvn-compiler.version}</version>
                <configuration>
                    <release>${java.version}</release>
                    <parameters>true</parameters>
                    <annotationProcessorPaths>
                        <path>
                            <groupId>org.projectlombok</groupId>
                            <artifactId>lombok</artifactId>
                            <version>${lombok.version}</version>
                        </path>
                        <path>
                            <groupId>org.mapstruct</groupId>
                            <artifactId>mapstruct-processor</artifactId>
                            <version>${mapstruct.version}</version>
                        </path>
                    </annotationProcessorPaths>
                </configuration>
            </plugin>
        </plugins>
    </build>

    <distributionManagement>
        <repository>
            <id>nexus-ci-releases</id>
            <url>https://nexus-ci.onefiserv.net/repository/mvn-na-issuer-distapp-coreservices-private-releases</url>
        </repository>
        <snapshotRepository>
            <id>nexus-ci-snapshots</id>
            <url>https://nexus-ci.onefiserv.net/repository/mvn-na-issuer-distapp-coreservices-private-snapshots</url>
        </snapshotRepository>
    </distributionManagement>
</project>




<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<settings xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.1.0 http://maven.apache.org/xsd/settings-1.1.0.xsd"
  xmlns="http://maven.apache.org/SETTINGS/1.1.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <localRepository>C:\Users\F2LIPBX\.m2\repository</localRepository>
  <servers>
    <server>
      <id>Nexus</id>
	<!--  <username>V9EIn6HT</username>
	<password>eIJ2Mcpm0WVl-2El4T0rvkNvtr_W5f7ZeGCz5SQkOJeB</password>-->
	<username>F2LIPBX</username>
	<password>Asb134798$wp</password>
    </server>
  </servers>
  <mirrors>
    <mirror>
      <id>Nexus</id>
      <mirrorOf>central</mirrorOf>
      <name>Nexus Proxy central</name>
      <url>https://nexus-dev.onefiserv.net/repository/Maven_Central/</url>
    </mirror>
    <mirror>
      <id>Nexus</id>
      <mirrorOf>jaspersoft-third-party</mirrorOf>
      <name>Nexus jaspersoft-third-party</name>
      <url>https://nexus-dev.onefiserv.net/repository/mvn-jfrog-jaspersoft-third-party-ce-artifacts</url>
    </mirror>
  </mirrors>
  <profiles>
    <profile>
      <id>nexus</id>
      <activation>
        <activeByDefault>true</activeByDefault>
      </activation>
      <repositories>
        <repository>
          <snapshots>
            <enabled>true</enabled>
          </snapshots>
          <id>Nexus</id>
          <name>maven</name>
          <url>https://nexus-dev.onefiserv.net/repository/mvn-gl-flume-public-group/</url>
        </repository>
      </repositories>
      <pluginRepositories>
        <pluginRepository>
          <snapshots>
            <enabled>false</enabled>
          </snapshots>
          <id>Nexus</id>
          <name>maven</name>
          <url>https://nexus-dev.onefiserv.net/repository/mvn-gl-flume-public-group/</url>
        </pluginRepository>
      </pluginRepositories>
    </profile>
  </profiles>
  <activeProfiles>
    <activeProfile>nexus</activeProfile>
  </activeProfiles>
</settings>

    <profile>
      <id>nexus-ci-repos</id>
      <activation>
        <activeByDefault>true</activeByDefault>
      </activation>
      <repositories>
        <repository>
          <id>nexus-ci-releases</id>
          <url>https://nexus-ci.onefiserv.net/repository/mvn-na-issuer-distapp-coreservices-private-releases</url>
          <releases><enabled>true</enabled></releases>
          <snapshots><enabled>false</enabled></snapshots>
        </repository>
        <repository>
          <id>nexus-ci-snapshots</id>
          <url>https://nexus-ci.onefiserv.net/repository/mvn-na-issuer-distapp-coreservices-private-snapshots</url>
          <releases><enabled>false</enabled></releases>
          <snapshots><enabled>true</enabled></snapshots>
        </repository>
      </repositories>
      <pluginRepositories>
        <pluginRepository>
          <id>nexus-ci-releases</id>
          <url>https://nexus-ci.onefiserv.net/repository/mvn-na-issuer-distapp-coreservices-private-releases</url>
          <releases><enabled>true</enabled></releases>
          <snapshots><enabled>false</enabled></snapshots>
        </pluginRepository>
        <pluginRepository>
          <id>nexus-ci-snapshots</id>
          <url>https://nexus-ci.onefiserv.net/repository/mvn-na-issuer-distapp-coreservices-private-snapshots</url>
          <releases><enabled>false</enabled></releases>
          <snapshots><enabled>true</enabled></snapshots>
        </pluginRepository>
      </pluginRepositories>
    </profile>


    <activeProfile>nexus-ci-repos</activeProfile>



