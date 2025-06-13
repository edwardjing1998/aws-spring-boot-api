Sonar Scanner CLI found in tool cache.
Run # Set the project key as the repo name and delete the org name
project_key: RAPIDadmin-microservices-java
INFO: Scanner configuration file: /home/runner/_work/_tool/sonar-scanner-5.0.1.3006-linux/conf/sonar-scanner.properties
INFO: Project root configuration file: NONE
INFO: SonarScanner 5.0.1.3006
INFO: Java 17.0.7 Eclipse Adoptium (64-bit)
INFO: Linux 5.15.0-1082-azure amd64
INFO: User cache: /home/runner/.sonar/cache
INFO: Analyzing on SonarQube server 2025.2.0.105476
INFO: Default locale: "en_US", source code encoding: "UTF-8"
INFO: Load global settings
INFO: Load global settings (done) | time=735ms
INFO: Server id: EA8D9556-AXCxQuQpSthpm3foey9n
WARN: sonar.plugins.downloadOnlyRequired is false, so ALL available plugins will be downloaded
INFO: Loading all plugins
INFO: Load plugins index
INFO: Load plugins index (done) | time=194ms
INFO: Load/download plugins
INFO: Load/download plugins (done) | time=401595ms
INFO: Loaded core extensions: developer-scanner, sca, server-common
INFO: Process project properties
INFO: Process project properties (done) | time=32ms
INFO: Execute project builders
INFO: Execute project builders (done) | time=4ms
INFO: Project key: RAPIDadmin-microservices-java
INFO: Base dir: /home/runner/_work/RAPIDadmin-microservices-java/RAPIDadmin-microservices-java
INFO: Working dir: /home/runner/_work/RAPIDadmin-microservices-java/RAPIDadmin-microservices-java/.scannerwork
INFO: Load project settings for component key: 'RAPIDadmin-microservices-java'
INFO: Load project settings for component key: 'RAPIDadmin-microservices-java' (done) | time=88ms
INFO: Load project branches
INFO: Load project branches (done) | time=79ms
INFO: Load branch configuration
INFO: Detected branch/PR in 'GitHub Action'
INFO: Auto-configuring branch 'rapid-case-service'
INFO: Load branch configuration (done) | time=5ms
INFO: Load quality profiles
INFO: Load quality profiles (done) | time=178ms
INFO: Auto-configuring with CI 'Github Actions'
INFO: Load active rules
INFO: Load active rules (done) | time=674ms
INFO: Load analysis cache
INFO: Load analysis cache | time=248ms
WARN: Property 'sonar.oe.file.suffixes' is not declared as multi-values/property set but was read using 'getStringArray' method. The SonarQube plugin declaring this property should be updated.
INFO: Branch name: rapid-case-service
WARN: The property 'sonar.login' is deprecated and will be removed in the future. Please use the 'sonar.token' property instead when passing a token.
INFO: Preprocessing files...
INFO: 1 language detected in 1 preprocessed file
INFO: 0 files ignored because of scm ignore settings
INFO: Load project repositories
INFO: Load project repositories (done) | time=330ms
INFO: Indexing files...
INFO: Project configuration:
INFO: 1 file indexed
INFO: Quality profile for java: Sonar way
INFO: ------------- Run sensors on module RAPIDadmin-microservices-java
INFO: Load metrics repository
INFO: Load metrics repository (done) | time=79ms
INFO: Sensor JavaSensor [java]
ERROR: Invalid value for 'sonar.java.binaries' property.
INFO: ------------------------------------------------------------------------
INFO: EXECUTION FAILURE
INFO: ------------------------------------------------------------------------
INFO: Total time: 7:31.994s
ERROR: Error during SonarScanner execution
INFO: Final Memory: 89M/300M
INFO: ------------------------------------------------------------------------
java.lang.IllegalStateException: No files nor directories matching 'target'
	at org.sonar.java.classpath.AbstractClasspath.getFilesFromProperty(AbstractClasspath.java:125)
	at org.sonar.java.classpath.ClasspathForMain.init(ClasspathForMain.java:53)
	at org.sonar.java.classpath.AbstractClasspath.getElements(AbstractClasspath.java:316)
	at org.sonar.java.SonarComponents.getJavaClasspath(SonarComponents.java:248)
	at org.sonar.java.JavaFrontend.<init>(JavaFrontend.java:92)
	at org.sonar.plugins.java.JavaSensor.execute(JavaSensor.java:111)
	at org.sonar.scanner.sensor.AbstractSensorWrapper.analyse(AbstractSensorWrapper.java:64)
	at org.sonar.scanner.sensor.ModuleSensorsExecutor.execute(ModuleSensorsExecutor.java:88)
	at org.sonar.scanner.sensor.ModuleSensorsExecutor.lambda$execute$1(ModuleSensorsExecutor.java:61)
	at org.sonar.scanner.sensor.ModuleSensorsExecutor.withModuleStrategy(ModuleSensorsExecutor.java:79)
	at org.sonar.scanner.sensor.ModuleSensorsExecutor.execute(ModuleSensorsExecutor.java:61)
	at org.sonar.scanner.scan.SpringModuleScanContainer.doAfterStart(SpringModuleScanContainer.java:82)
	at org.sonar.core.platform.SpringComponentContainer.startComponents(SpringComponentContainer.java:227)
	at org.sonar.core.platform.SpringComponentContainer.execute(SpringComponentContainer.java:206)
	at org.sonar.scanner.scan.SpringProjectScanContainer.scan(SpringProjectScanContainer.java:212)
	at org.sonar.scanner.scan.SpringProjectScanContainer.scanRecursively(SpringProjectScanContainer.java:208)
	at org.sonar.scanner.scan.SpringProjectScanContainer.doAfterStart(SpringProjectScanContainer.java:178)
	at org.sonar.core.platform.SpringComponentContainer.startComponents(SpringComponentContainer.java:227)
	at org.sonar.core.platform.SpringComponentContainer.execute(SpringComponentContainer.java:206)
	at org.sonar.scanner.bootstrap.SpringScannerContainer.doAfterStart(SpringScannerContainer.java:339)
	at org.sonar.core.platform.SpringComponentContainer.startComponents(SpringComponentContainer.java:227)
	at org.sonar.core.platform.SpringComponentContainer.execute(SpringComponentContainer.java:206)
	at org.sonar.scanner.bootstrap.SpringGlobalContainer.doAfterStart(SpringGlobalContainer.java:142)
	at org.sonar.core.platform.SpringComponentContainer.startComponents(SpringComponentContainer.java:227)
	at org.sonar.core.platform.SpringComponentContainer.execute(SpringComponentContainer.java:206)
	at org.sonar.batch.bootstrapper.Batch.doExecute(Batch.java:73)
	at org.sonar.batch.bootstrapper.Batch.execute(Batch.java:67)
	at org.sonarsource.scanner.api.internal.batch.BatchIsolatedLauncher.execute(BatchIsolatedLauncher.java:46)
	at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke(Unknown Source)
	at java.base/jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke(Unknown Source)
	at java.base/java.lang.reflect.Method.invoke(Unknown Source)
	at org.sonarsource.scanner.api.internal.IsolatedLauncherProxy.invoke(IsolatedLauncherProxy.java:60)
	at jdk.proxy1/jdk.proxy1.$Proxy0.execute(Unknown Source)
	at org.sonarsource.scanner.api.EmbeddedScanner.doExecute(EmbeddedScanner.java:189)
	at org.sonarsource.scanner.api.EmbeddedScanner.execute(EmbeddedScanner.java:138)
	at org.sonarsource.scanner.cli.Main.execute(Main.java:126)
	at org.sonarsource.scanner.cli.Main.execute(Main.java:81)
	at org.sonarsource.scanner.cli.Main.main(Main.java:62)
ERROR: 
ERROR: Re-run SonarScanner using the -X switch to enable full debug logging.
Error: Process completed with exit code 1.
