# This workflow will build a Maven project and 
# upload the artifact to Nexus Repository
# 

name: Build Maven Project
on:
  push:
    # It is recommended to build from a single integration branch such as main.
    # https://docs.github.com/en/actions/writing-workflows/choosing-when-your-workflow-runs/triggering-a-workflow
    branches:
      - rapid-admin-j
  pull_request:
    # branches: 
    #   - main

  # Defining inputs for manually triggered workflows - https://docs.github.com/en/actions/writing-workflows/choosing-when-your-workflow-runs/triggering-a-workflow#defining-inputs-for-manually-triggered-workflows
  workflow_dispatch:
jobs:
  ci-workflow:
    # Do not change. This is the reusable workflow to build the maven project
    uses: fiserv/flume-reuseable-workflows/.github/workflows/maven.yml@main
    # Do not change. This enables to inherit common secrets from the organization settings
    secrets: inherit
    with:
      # --- REQUIRED PARAMETERS --- 
      # APM Number for the application from AppMap
      apm: APM0001099 

      # Application Name. Overrides value in pom.xml
      app_name: RAPIDadmin-microservices-java
      # Application Version. Overrides value in pom.xml
      app_version: 0.0.1-SNAPSHOT
      # Append Build number to the version
      # app_version: 1.0.${{ github.run_number }}-SNAPSHOT
      
      # --- OPTIONAL PARAMETERS ---
      # UAID of the application from CMDB
      # uaid: 
      
      # A specific runner to build the project 
      #build_runner_name: 'arc-scale-set'
      
      # Java Version for available version refer: 
      # https://enterprise-confluence.onefiserv.net/display/BSDevOpsCOE/Maven+Builds#Supported%20Maven+&+Java+Versions
      java_version: '21'
      # Maven Version
      # maven_version: '3.9.6'
      
      # By Default the workflow executes mvn test but if you want to run mvn verify please specify the test args to verify
      # test_args: test
      
      # Publish Repostories can be updated to your target repositories
      # nexus_snapshot_repo: mvn-gl-flume-public-snapshots
      # nexus_release_repo: mvn-gl-flume-public-releases

      # Enable or disable SonaQube Scans
      # sonar_enable: true
      # sonar_sourcepath: src/main/java
      sonar_args: '-Dsonar.java.binaries=target'
      
      # Enable FOP
      # fop_enable: true

      # fop_application: If your FOP Application is not the same as your APM, please specify the FOP Application name
      # fop_application:

      # fop_version: If your FOP Version is not the same as your App Version, please specify the FOP Version
      # fop_version:

      # enable Sonatype
      # sonatype_enable: true

      # sonatype_application: If your Sonatype Application is not the same as your APM or FOP Application , please specify the Sonatype Application name
      # sonatype_application:

      # sonatype_version: If your Sonatype Version is not the same as your App Version or FOP Version, please specify the Sonatype Version
      # sonatype_version:

      # publish artifact is set to false by default
      # publish_artifact: true

      # --- CONTAINERIZE ---
      # The following fields only apply if the application needs to be built as a docker image
      # The project should contain a Dockerfile
      
      # Image Name
      image_name: edwardjing/review-deleted-case
      # Image Tags
      image_tag:  ${{ github.sha }}

      # PLEASE REFER https://enterprise-confluence.onefiserv.net/display/BSDevOpsCOE/Maven+Builds for all avalable parameters





      #10 [builder 4/6] RUN mvn dependency:go-offline -B
#10 1.750 [INFO] Scanning for projects...
#10 2.019 [INFO] Downloading from central: https://repo.maven.apache.org/maven2/org/springframework/boot/spring-boot-starter-parent/3.5.0/spring-boot-starter-parent-3.5.0.pom
Error: 57 [ERROR] [ERROR] Some problems were encountered while processing the POMs:
#10 2.657 [FATAL] Non-resolvable parent POM for admin:admin:0.0.1-SNAPSHOT: The following artifacts could not be resolved: org.springframework.boot:spring-boot-starter-parent:pom:3.5.0 (absent): Could not transfer artifact org.springframework.boot:spring-boot-starter-parent:pom:3.5.0 from/to central (https://repo.maven.apache.org/maven2): Remote host terminated the handshake and 'parent.relativePath' points at no local POM @ line 4, column 11
#10 2.657  @ 
#10 2.657 [ERROR] The build could not read 1 project -> [Help 1]
#10 2.657 [ERROR]   
#10 2.658 [ERROR]   The project admin:admin:0.0.1-SNAPSHOT (/workspace/pom.xml) has 1 error
#10 2.658 [ERROR]     Non-resolvable parent POM for admin:admin:0.0.1-SNAPSHOT: The following artifacts could not be resolved: org.springframework.boot:spring-boot-starter-parent:pom:3.5.0 (absent): Could not transfer artifact org.springframework.boot:spring-boot-starter-parent:pom:3.5.0 from/to central (https://repo.maven.apache.org/maven2): Remote host terminated the handshake and 'parent.relativePath' points at no local POM @ line 4, column 11: SSL peer shut down incorrectly -> [Help 2]
#10 2.658 [ERROR] 
#10 2.659 [ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
#10 2.659 [ERROR] Re-run Maven using the -X switch to enable full debug logging.
#10 2.659 [ERROR] 
#10 2.659 [ERROR] For more information about the errors and possible solutions, please read the following articles:
#10 2.660 [ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/ProjectBuildingException
#10 2.660 [ERROR] [Help 2] http://cwiki.apache.org/confluence/display/MAVEN/UnresolvableModelException
#10 ERROR: process "/bin/sh -c mvn dependency:go-offline -B" did not complete successfully: exit code: 1

#7 [stage-1 1/3] FROM docker.io/library/eclipse-temurin:21-jdk@sha256:5e7983a70405b3e40f79592276e73c81215fd9822bf37a66e325fb87a6faa57d
#7 sha256:fdf9441e6dddb8defe5d23ab009dd1a3d91ad4f953b4d53d9e904e1adcae03de 143.65MB / 157.65MB 19.4s
------
 > [builder 4/6] RUN mvn dependency:go-offline -B:
Error: ERROR]   
Error: ERROR]   The project admin:admin:0.0.1-SNAPSHOT (/workspace/pom.xml) has 1 error
Error: ERROR]     Non-resolvable parent POM for admin:admin:0.0.1-SNAPSHOT: The following artifacts could not be resolved: org.springframework.boot:spring-boot-starter-parent:pom:3.5.0 (absent): Could not transfer artifact org.springframework.boot:spring-boot-starter-parent:pom:3.5.0 from/to central (https://repo.maven.apache.org/maven2): Remote host terminated the handshake and 'parent.relativePath' points at no local POM @ line 4, column 11: SSL peer shut down incorrectly -> [Help 2]
Error: ERROR] 
Error: ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
Error: ERROR] Re-run Maven using the -X switch to enable full debug logging.
Error: ERROR] 
Error: ERROR] For more information about the errors and possible solutions, please read the following articles:
Error: ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/ProjectBuildingException
Error: ERROR] [Help 2] http://cwiki.apache.org/confluence/display/MAVEN/UnresolvableModelException
------
Dockerfile:9
--------------------
   7 |     
   8 |     # Pre-fetch all dependencies
   9 | >>> RUN mvn dependency:go-offline -B
  10 |     
  11 |     # Copy your sources and build the “fat” JAR
--------------------
ERROR: failed to solve: process "/bin/sh -c mvn dependency:go-offline -B" did not complete successfully: exit code: 1
Error: Process completed with exit code 1.
