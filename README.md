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





# ─── Build stage ───────────────────────────────────────────────────────────────
FROM maven:3.9.6-eclipse-temurin-21 AS builder
WORKDIR /workspace

# 1) Inject your corporate Maven settings so that Maven can reach your Nexus/mirror
COPY .github/maven/settings.xml /root/.m2/settings.xml

# 2) Copy just the POM and pre-fetch dependencies (including the Spring Boot parent)
COPY pom.xml .
RUN mvn dependency:go-offline -B

# 3) Copy sources and package the “fat” JAR
COPY src/ src/
RUN mvn package -DskipTests -B

# ─── Runtime stage ─────────────────────────────────────────────────────────────
FROM eclipse-temurin:21-jdk
WORKDIR /app

# 4) Copy the assembled JAR from the builder
COPY --from=builder /workspace/target/admin-0.0.1-SNAPSHOT.jar app.jar

ENTRYPOINT ["java", "-jar", "/app/app.jar"]








#12 135.8 [ERROR] /workspace/src/main/java/rapid/deletedcasereview/config/CaseDataGenerator.java:[5,46] package org.springframework.context.annotation does not exist
#12 135.8 [ERROR] /workspace/src/main/java/rapid/deletedcasereview/config/CaseDataGenerator.java:[6,38] package org.springframework.stereotype does not exist
#12 135.8 [ERROR] /workspace/src/main/java/rapid/deletedcasereview/config/CaseDataGenerator.java:[14,2] cannot find symbol
#12 135.8   symbol: class Component
#12 135.8 [ERROR] /workspace/src/main/java/rapid/deletedcasereview/config/CaseDataGenerator.java:[15,2] cannot find symbol
#12 135.8   symbol: class AllArgsConstructor
#12 135.8 [ERROR] /workspace/src/main/java/rapid/deletedcasereview/persistence/repository/CaseRepository.java:[3,47] package org.springframework.data.jpa.repository does not exist
#12 135.8 [ERROR] /workspace/src/main/java/rapid/deletedcasereview/persistence/repository/CaseRepository.java:[4,38] package org.springframework.stereotype does not exist
#12 135.8 [ERROR] /workspace/src/main/java/rapid/deletedcasereview/persistence/repository/CaseRepository.java:[9,41] cannot find symbol
#12 135.8   symbol: class JpaRepository
#12 135.8 [ERROR] /workspace/src/main/java/rapid/deletedcasereview/persistence/repository/CaseRepository.java:[8,2] cannot find symbol
#12 135.8   symbol: class Repository
#12 135.8 [ERROR] /workspace/src/main/java/rapid/deletedcasereview/persistence/entity/Case.java:[4,14] package lombok does not exist
#12 135.8 [ERROR] /workspace/src/main/java/rapid/deletedcasereview/persistence/entity/Case.java:[5,14] package lombok does not exist
#12 135.8 [ERROR] /workspace/src/main/java/rapid/deletedcasereview/persistence/entity/Case.java:[11,2] cannot find symbol
#12 135.8   symbol: class Entity
#12 135.8 [ERROR] /workspace/src/main/java/rapid/deletedcasereview/persistence/entity/Case.java:[12,2] cannot find symbol
#12 135.8   symbol: class Data
#12 135.8 [ERROR] /workspace/src/main/java/rapid/deletedcasereview/persistence/entity/Case.java:[13,2] cannot find symbol
#12 135.8   symbol: class Table
#12 135.8 [ERROR] /workspace/src/main/java/rapid/deletedcasereview/persistence/entity/Case.java:[14,2] cannot find symbol
#12 135.8   symbol: class NoArgsConstructor
#12 135.8 [ERROR] /workspace/src/main/java/rapid/deletedcasereview/persistence/repository/AddressRepository.java:[3,47] package org.springframework.data.jpa.repository does not exist
#12 135.8 [ERROR] /workspace/src/main/java/rapid/deletedcasereview/persistence/repository/AddressRepository.java:[4,38] package org.springframework.stereotype does not exist
#12 135.8 [ERROR] /workspace/src/main/java/rapid/deletedcasereview/persistence/repository/AddressRepository.java:[12,44] cannot find symbol
#12 135.8   symbol: class JpaRepository
------
Dockerfile:12
--------------------
  10 |     
  11 |     # Pre-fetch all dependencies

