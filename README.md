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






<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>trace-client-sysprin-service</groupId>
    <artifactId>trace-client-sysprin-service</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>trace-client-sysprin-service</name>
    <description>trace-client-sysprin-service</description>
    <packaging>pom</packaging>

    <modules>
        <module>client-sysprin-writer</module>
        <module>client-sysprin-reader</module>
        <module>common-model</module>
        <module>common-mapper</module>
        <module>common-api-dto</module>
        <module>gateway</module>
        <module>search-integration</module>
    </modules>

    <properties>
        <java.version>21</java.version>
        <mvn-compiler.version>3.13.0</mvn-compiler.version>
        <mvn-plugin.version>3.4.2</mvn-plugin.version>
        <mapstruct.version>1.5.5.Final</mapstruct.version>
        <lombok.version>1.18.38</lombok.version>
        <openapi.version>2.8.8</openapi.version>
        <spring-boot.version>3.5.0</spring-boot.version>
        <spring-cloud.version>2025.0.0</spring-cloud.version>
        <lucene.version>8.11.2</lucene.version>
    </properties>

    <scm>
        <developerConnection>scm:git:git@gitlab.onefiserv.net:issuers/fos-modernization/plastic/rapid/RAPID-Client-microservices-Java.git</developerConnection>
        <tag>HEAD</tag>
    </scm>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-dependencies</artifactId>
                <version>3.5.0</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <pluginManagement>
            <plugins>
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>3.13.0</version>
                    <configuration>
                        <release>${java.version}</release>
                        <parameters>true</parameters>
                        <annotationProcessorPaths>
                            <path>
                                <groupId>org.projectlombok</groupId>
                                <artifactId>lombok</artifactId>
                                <version>1.18.38</version>
                            </path>
                        </annotationProcessorPaths>
                    </configuration>
                </plugin>

                <plugin>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-maven-plugin</artifactId>
                    <version>3.4.2</version>
                    <executions>
                        <execution>
                            <goals>
                                <goal>repackage</goal>
                            </goals>
                        </execution>
                    </executions>
                </plugin>

                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-jar-plugin</artifactId>
                    <version>3.4.2</version>
                </plugin>
            </plugins>
        </pluginManagement>
    </build>

    <distributionManagement>
        <repository>
            <id>nexus-ci-releases</id>
            <url>https://nexus-ci.onefiserv.net/repository/mvn-na-issuer-distapp-coreservices-private-releases</url>
        </repository>
    </distributionManagement>

</project>




