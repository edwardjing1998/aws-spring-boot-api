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

