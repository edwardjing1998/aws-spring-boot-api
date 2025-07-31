<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.fiserv.dda</groupId>
    <artifactId>dda-billing-manager</artifactId>
    <version>0.0.1-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>17</maven.compiler.source>
        <maven.compiler.target>17</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <nodeVersion>18.17.1</nodeVersion>
    </properties>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.2.12</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-security</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.security</groupId>
            <artifactId>spring-security-oauth2-client</artifactId>
            <exclusions>
                <exclusion>
                    <groupId>org.springframework.security</groupId>
                    <artifactId>spring-security-core</artifactId>
                </exclusion>
                <exclusion>
                    <artifactId>json-smart</artifactId>
                    <groupId>net.minidev</groupId>
                </exclusion>
                <exclusion>
                    <groupId>org.springframework.security</groupId>
                    <artifactId>spring-security-crypto</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>org.springframework.security</groupId>
            <artifactId>spring-security-core</artifactId>
            <exclusions>
            <exclusion>
                <groupId>org.springframework.security</groupId>
                <artifactId>spring-security-crypto</artifactId>
            </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>org.springframework.security</groupId>
            <artifactId>spring-security-crypto</artifactId>
            <version>6.4.4</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-web</artifactId>
            <version>6.1.21</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.security</groupId>
            <artifactId>spring-security-oauth2-jose</artifactId>
        </dependency>
        <dependency>
            <groupId>io.pivotal.cfenv</groupId>
            <artifactId>java-cfenv-boot-pivotal-sso</artifactId>
            <version>3.2.0</version>
            <exclusions>
                <exclusion>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-starter-parent</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <exclusions>
                <exclusion>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-starter-tomcat</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
            <exclusions>
                <exclusion>
                    <groupId>org.apache.tomcat.embed</groupId>
                    <artifactId>tomcat-embed-core</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>org.apache.tomcat.embed</groupId>
            <artifactId>tomcat-embed-core</artifactId>
            <version>10.1.42</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-configuration-processor</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
            <exclusions>
                <exclusion>
                    <artifactId>netty-handler</artifactId>
                    <groupId>io.netty</groupId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-validation</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-mail</artifactId>
            <exclusions>
                <exclusion>
                    <groupId>org.yaml</groupId>
                    <artifactId>snakeyaml</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>org.springdoc</groupId>
            <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
            <version>2.5.0</version>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>
        <dependency>
            <groupId>com.zaxxer</groupId>
            <artifactId>HikariCP</artifactId>
        </dependency>
        <dependency>
            <groupId>com.mysql</groupId>
            <artifactId>mysql-connector-j</artifactId>
            <version>8.2.0</version>
        </dependency>
        <dependency>
            <groupId>org.redisson</groupId>
            <artifactId>redisson</artifactId>
            <version>3.33.0</version>
            <exclusions>
                <exclusion>
                    <artifactId>netty-handler</artifactId>
                    <groupId>io.netty</groupId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>com.google.guava</groupId>
            <artifactId>guava</artifactId>
            <version>33.3.0-jre</version>
        </dependency>
        <dependency>
            <groupId>com.firstdata.fs</groupId>
            <artifactId>gfs-cobol-utilities</artifactId>
            <version>1.52.65</version>
        </dependency>
        <dependency>
            <groupId>com.jcraft</groupId>
            <artifactId>jsch</artifactId>
            <version>0.1.55</version>
        </dependency>
        <dependency>
            <groupId>ch.qos.logback</groupId>
            <artifactId>logback-classic</artifactId>
            <version>1.5.18</version>
        </dependency>
        <dependency>
            <groupId>ch.qos.logback</groupId>
            <artifactId>logback-core</artifactId>
            <version>1.5.18</version>
        </dependency>
        <dependency>
            <groupId>org.yaml</groupId>
            <artifactId>snakeyaml</artifactId>
            <version>2.2</version>
        </dependency>
        <dependency>
            <groupId>com.github.fppt</groupId>
            <artifactId>jedis-mock</artifactId>
            <version>1.0.11</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.mockito</groupId>
            <artifactId>mockito-core</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter-api</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.xmlunit</groupId>
                    <artifactId>xmlunit-core</artifactId>
                </exclusion>
                <exclusion>
                    <artifactId>json-smart</artifactId>
                    <groupId>net.minidev</groupId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>commons-io</groupId>
            <artifactId>commons-io</artifactId>
            <version>2.16.1</version>
        </dependency>
        <dependency>
            <groupId>com.opencsv</groupId>
            <artifactId>opencsv</artifactId>
            <version>5.10</version>
            <exclusions>
                <exclusion>
                    <artifactId>commons-text</artifactId>
                    <groupId>org.apache.commons</groupId>
                </exclusion>
                <exclusion>
                    <artifactId>commons-collections</artifactId>
                    <groupId>commons-collections</groupId>
                </exclusion>
                <exclusion>
                    <artifactId>commons-beanutils</artifactId>
                    <groupId>commons-beanutils</groupId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-collections4</artifactId>
            <version>4.4</version>
        </dependency>
        <dependency>
            <groupId>commons-beanutils</groupId>
            <artifactId>commons-beanutils</artifactId>
            <version>1.11.0</version>
            <exclusions>
                <exclusion>
                    <artifactId>commons-collections</artifactId>
                    <groupId>commons-collections</groupId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>com.h2database</groupId>
            <artifactId>h2</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>jakarta.servlet</groupId>
            <artifactId>jakarta.servlet-api</artifactId>
        </dependency>
        <dependency>
            <groupId>net.minidev</groupId>
            <artifactId>json-smart</artifactId>
            <version>2.5.2</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
        </dependency>
        <dependency>
            <groupId>io.netty</groupId>
            <artifactId>netty-handler</artifactId>
            <version>4.1.118.Final</version>
        </dependency>
    </dependencies>
    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-install-plugin</artifactId>
                <executions>
                    <execution>
                        <id>install-external</id>
                        <phase>clean</phase>
                        <configuration>
                            <file>${basedir}/external/cobol-utilities.jar</file>
                            <groupId>com.firstdata.fs</groupId>
                            <artifactId>gfs-cobol-utilities</artifactId>
                            <version>1.52.65</version>
                            <packaging>jar</packaging>
                            <generatePom>true</generatePom>
                        </configuration>
                        <goals>
                            <goal>install-file</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-jar-plugin</artifactId>
                <version>3.4.2</version>
            </plugin>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <dependencies>
                    <dependency>
                        <groupId>org.codehaus.plexus</groupId>
                        <artifactId>plexus-archiver</artifactId>
                        <version>4.9.2</version>
                    </dependency>
                </dependencies>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.11.0</version>
                <configuration>
                    <release>17</release>
                    <annotationProcessorPaths>
                        <path>
                            <groupId>org.projectlombok</groupId>
                            <artifactId>lombok</artifactId>
                            <version>1.18.32</version>
                        </path>
                    </annotationProcessorPaths>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.sonarsource.scanner.maven</groupId>
                <artifactId>sonar-maven-plugin</artifactId>
                <version>5.0.0.4389</version>
                  <dependencies>
                    <dependency>
                        <groupId>org.codehaus.plexus</groupId>
                        <artifactId>plexus-utils</artifactId>
                        <version>4.0.0</version>
                    </dependency>
                  </dependencies>
            </plugin>
        </plugins>
    </build>
    <profiles>
        <profile>
            <id>Windows</id>
            <activation>
                <os>
                    <family>Windows</family>
                </os>
            </activation>
            <build>
                <plugins>
                    <plugin>
                        <groupId>org.springframework.boot</groupId>
                        <artifactId>spring-boot-maven-plugin</artifactId>
                    </plugin>
<!--                    <plugin>-->
<!--                        <groupId>org.codehaus.mojo</groupId>-->
<!--                        <artifactId>exec-maven-plugin</artifactId>-->
<!--                        <version>3.1.0</version>-->
<!--                        <executions>-->
<!--                            <execution>-->
<!--                                <id>build-react</id>-->
<!--                                <phase>generate-sources</phase>-->
<!--                                <goals>-->
<!--                                    <goal>exec</goal>-->
<!--                                </goals>-->
<!--                            </execution>-->
<!--                        </executions>-->
<!--                        <configuration>-->
<!--                            <executable>npm</executable>-->
<!--                            <workingDirectory>${project.basedir}/billing-manager-ui</workingDirectory>-->
<!--                            <commandlineArgs>run build</commandlineArgs>-->
<!--                        </configuration>-->
<!--                    </plugin>-->
                </plugins>
            </build>
        </profile>
        <profile>
            <id>Other</id>
            <activation>
                <os>
                    <family>!Windows</family>
                </os>
            </activation>
            <build>
                <plugins>
                    <plugin>
                        <groupId>org.springframework.boot</groupId>
                        <artifactId>spring-boot-maven-plugin</artifactId>
                    </plugin>
<!--                    <plugin>-->
<!--                        <groupId>com.github.eirslett</groupId>-->
<!--                        <artifactId>frontend-maven-plugin</artifactId>-->
<!--                        <version>1.15.0</version>-->
<!--                        <configuration>-->
<!--                            <workingDirectory>${project.basedir}/billing-manager-ui/</workingDirectory>-->
<!--                            &lt;!&ndash; where to install npm &ndash;&gt;-->
<!--                            <installDirectory>${project.build.directory}/install</installDirectory>-->
<!--                            <nodeDownloadRoot>https://nexus-dev.onefiserv.net/repository/raw-gl-flume-public-vendor/flume-tools/nodejs/</nodeDownloadRoot>-->
<!--                            <npmDownloadRoot>https://nexus-dev.onefiserv.net/repository/npm-registry/</npmDownloadRoot>-->
<!--                            <serverId>Nexus</serverId>-->
<!--                            <npmInheritsProxyConfigFromMaven>false</npmInheritsProxyConfigFromMaven>-->
<!--                            <bowerInheritsProxyConfigFromMaven>false</bowerInheritsProxyConfigFromMaven>-->
<!--                            <npmRegistryURL>https://nexus-dev.onefiserv.net/repository/npm-registry/</npmRegistryURL>-->
<!--                        </configuration>-->
<!--                        <executions>-->
<!--                            <execution>-->
<!--                                <id>install-node-and-npm</id>-->
<!--                                <goals>-->
<!--                                    <goal>install-node-and-npm</goal>-->
<!--                                </goals>-->
<!--                                <configuration>-->
<!--                                    <nodeVersion>v${nodeVersion}</nodeVersion>-->
<!--                                </configuration>-->
<!--                            </execution>-->
<!--                            <execution>-->
<!--                                <id>install-node_modules</id>-->
<!--                                <goals>-->
<!--                                    <goal>npm</goal>-->
<!--                                </goals>-->
<!--                                <configuration>-->
<!--                                    <arguments>install</arguments>-->
<!--                                </configuration>-->
<!--                            </execution>-->
<!--                            <execution>-->
<!--                                <id>build-react-project</id>-->
<!--                                <goals>-->
<!--                                    <goal>npm</goal>-->
<!--                                </goals>-->
<!--                                <configuration>-->
<!--                                    <arguments>run build</arguments>-->
<!--                                </configuration>-->
<!--                                <phase>generate-resources</phase>-->
<!--                            </execution>-->
<!--                        </executions>-->
<!--                    </plugin>-->
                    <plugin>
                        <groupId>org.apache.maven.plugins</groupId>
                        <artifactId>maven-antrun-plugin</artifactId>
                        <version>3.1.0</version>
<!--                        <executions>-->
<!--                            <execution>-->
<!--                                <phase>process-resources</phase>-->
<!--                                <id>delete-node_modules-and-node-installation</id>-->
<!--                                <goals>-->
<!--                                    <goal>run</goal>-->
<!--                                </goals>-->
<!--                                <configuration>-->
<!--                                    <target>-->
<!--                                        <delete-->
<!--                                                dir="${project.build.directory}/install"-->
<!--                                                includeemptydirs="true"/>-->
<!--                                    </target>-->
<!--                                </configuration>-->
<!--                            </execution>-->
<!--                        </executions>-->
                    </plugin>
                </plugins>
            </build>
        </profile>
    </profiles>
</project>
