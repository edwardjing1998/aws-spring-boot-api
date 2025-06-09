# This workflow will build a Maven project and 
# upload the artifact to Nexus Repository
# 

name: Build Maven Project
on:
  push:
    # It is recommended to build from a single integration branch such as main.
    # https://docs.github.com/en/actions/writing-workflows/choosing-when-your-workflow-runs/triggering-a-workflow
    branches:
      - deleted-case-j
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
      sonar_enable: true
      sonar_sourcepath: src/main/java
      sonar_args: '-Dsonar.java.binaries=target'
      
      # Enable FOP
      # fop_enable: true

      # fop_application: If your FOP Application is not the same as your APM, please specify the FOP Application name
      # fop_application:

      # fop_version: If your FOP Version is not the same as your App Version, please specify the FOP Version
      # fop_version:

      # enable Sonatype
      sonatype_enable: true

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
