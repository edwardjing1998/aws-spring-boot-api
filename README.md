<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
		 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		 xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>3.5.0</version> <!-- ✅ Use stable version -->
		<relativePath/> <!-- lookup parent from repository -->
	</parent>

	<groupId>admin</groupId>
	<artifactId>admin</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<name>admin</name>
	<description>Rapid Admin</description>

	<properties>
		<java.version>21</java.version>
	</properties>

	<dependencies>
		<!-- Exclude tomcat-embed-core -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

		<!-- Spring Data JPA -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-data-jpa</artifactId>
		</dependency>

		<!-- H2 In-Memory Database -->
		<dependency>
			<groupId>com.h2database</groupId>
			<artifactId>h2</artifactId>
			<scope>runtime</scope>
		</dependency>

		<!-- Liquibase -->
		<dependency>
			<groupId>org.liquibase</groupId>
			<artifactId>liquibase-core</artifactId>
		</dependency>

		<!-- ✅ Lombok -->
		<dependency>
			<groupId>org.projectlombok</groupId>
			<artifactId>lombok</artifactId>
			<version>1.18.30</version> <!-- latest stable as of early 2025 -->
			<scope>provided</scope>
		</dependency>

		<dependency>
			<groupId>org.springdoc</groupId>
			<artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
			<version>2.2.0</version>
		</dependency>

		<dependency>
			<groupId>com.microsoft.sqlserver</groupId>
			<artifactId>mssql-jdbc</artifactId>
			<version>12.10.0.jre11</version> <!-- Match the DLL version -->
		</dependency>

		<!-- Spring Boot Starter for caching -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-cache</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-jms</artifactId>
		</dependency>

		<!-- Caffeine cache library -->
		<dependency>
			<groupId>com.github.ben-manes.caffeine</groupId>
			<artifactId>caffeine</artifactId>
		</dependency>

		<dependency>
			<groupId>org.apache.lucene</groupId>
			<artifactId>lucene-core</artifactId>
			<version>8.11.2</version>
		</dependency>
		<dependency>
			<groupId>org.apache.lucene</groupId>
			<artifactId>lucene-analyzers-common</artifactId>
			<version>8.11.2</version>
		</dependency>

		<dependency>
			<groupId>org.apache.lucene</groupId>
			<artifactId>lucene-queryparser</artifactId>
			<version>8.11.4</version>
		</dependency>
	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-compiler-plugin</artifactId>
				<version>3.13.0</version>
				<configuration>
					    <release>${java.version}</release>
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
		</plugins>
	</build>

</project>
