2025-08-28T18:33:07.705-05:00  INFO 9592 --- [case-reader] [           main] j.LocalContainerEntityManagerFactoryBean : Initialized JPA EntityManagerFactory for persistence unit 'default'
2025-08-28T18:33:09.237-05:00  INFO 9592 --- [case-reader] [           main] o.s.d.j.r.query.QueryEnhancerFactory     : Hibernate is in classpath; If applicable, HQL parser will be used.
2025-08-28T18:33:11.955-05:00  WARN 9592 --- [case-reader] [           main] ConfigServletWebServerApplicationContext : Exception encountered during context initialization - cancelling refresh attempt: org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating bean with name 'caseDataGenerator' defined in file [C:\Users\F2LIPBX\spring_boot\kalpana\trace-cases\case-reader\target\classes\rapid\cases\config\data\CaseDataGenerator.class]: Unsatisfied dependency expressed through constructor parameter 2: Error creating bean with name 'accountTransactionRepo' defined in rapid.repository.cases.accountTransaction.AccountTransactionRepo defined in @EnableJpaRepositories declared on CaseReaderApplication: Could not create query for public abstract java.util.List rapid.repository.cases.accountTransaction.AccountTransactionRepo.findByAccNumOrdered(java.lang.String); Reason: Validation failed for query for method public abstract java.util.List rapid.repository.cases.accountTransaction.AccountTransactionRepo.findByAccNumOrdered(java.lang.String)
2025-08-28T18:33:11.957-05:00  INFO 9592 --- [case-reader] [           main] j.LocalContainerEntityManagerFactoryBean : Closing JPA EntityManagerFactory for persistence unit 'default'
2025-08-28T18:33:11.968-05:00  INFO 9592 --- [case-reader] [           main] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Shutdown initiated...
2025-08-28T18:33:11.981-05:00  INFO 9592 --- [case-reader] [           main] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Shutdown completed.
2025-08-28T18:33:11.992-05:00  INFO 9592 --- [case-reader] [           main] o.apache.catalina.core.StandardService   : Stopping service [Tomcat]
2025-08-28T18:33:12.078-05:00  INFO 9592 --- [case-reader] [           main] .s.b.a.l.ConditionEvaluationReportLogger :

Error starting ApplicationContext. To display the condition evaluation report re-run your application with 'debug' enabled.
2025-08-28T18:33:12.185-05:00 ERROR 9592 --- [case-reader] [           main] o.s.boot.SpringApplication               : Application run failed

org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating bean with name 'caseDataGenerator' defined in file [C:\Users\F2LIPBX\spring_boot\kalpana\trace-cases\case-reader\target\classes\rapid\cases\config\data\CaseDataGenerator.class]: Unsatisfied dependency expressed through constructor parameter 2: Error creating bean with name 'accountTransactionRepo' defined in rapid.repository.cases.accountTransaction.AccountTransactionRepo defined in @EnableJpaRepositories declared on CaseReaderApplication: Could not create query for public abstract java.util.List rapid.repository.cases.accountTransaction.AccountTransactionRepo.findByAccNumOrdered(java.lang.String); Reason: Validation failed for query for method public abstract java.util.List rapid.repository.cases.accountTransaction.AccountTransactionRepo.findByAccNumOrdered(java.lang.String)
        at org.springframework.beans.factory.support.ConstructorResolver.createArgumentArray(ConstructorResolver.java:804) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.ConstructorResolver.autowireConstructor(ConstructorResolver.java:240) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.autowireConstructor(AbstractAutowireCapableBeanFactory.java:1395) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBeanInstance(AbstractAutowireCapableBeanFactory.java:1232) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.doCreateBean(AbstractAutowireCapableBeanFactory.java:569) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBean(AbstractAutowireCapableBeanFactory.java:529) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.AbstractBeanFactory.lambda$doGetBean$0(AbstractBeanFactory.java:339) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.DefaultSingletonBeanRegistry.getSingleton(DefaultSingletonBeanRegistry.java:373) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.AbstractBeanFactory.doGetBean(AbstractBeanFactory.java:337) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.AbstractBeanFactory.getBean(AbstractBeanFactory.java:202) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.DefaultListableBeanFactory.instantiateSingleton(DefaultListableBeanFactory.java:1222) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.DefaultListableBeanFactory.preInstantiateSingleton(DefaultListableBeanFactory.java:1188) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.DefaultListableBeanFactory.preInstantiateSingletons(DefaultListableBeanFactory.java:1123) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.context.support.AbstractApplicationContext.finishBeanFactoryInitialization(AbstractApplicationContext.java:987) ~[spring-context-6.2.7.jar:6.2.7]
        at org.springframework.context.support.AbstractApplicationContext.refresh(AbstractApplicationContext.java:627) ~[spring-context-6.2.7.jar:6.2.7]
        at org.springframework.boot.web.servlet.context.ServletWebServerApplicationContext.refresh(ServletWebServerApplicationContext.java:146) ~[spring-boot-3.5.0.jar:3.5.0]
        at org.springframework.boot.SpringApplication.refresh(SpringApplication.java:753) ~[spring-boot-3.5.0.jar:3.5.0]
        at org.springframework.boot.SpringApplication.refreshContext(SpringApplication.java:439) ~[spring-boot-3.5.0.jar:3.5.0]
        at org.springframework.boot.SpringApplication.run(SpringApplication.java:318) ~[spring-boot-3.5.0.jar:3.5.0]
        at rapid.cases.CaseReaderApplication.main(CaseReaderApplication.java:21) ~[classes/:na]
Caused by: org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'accountTransactionRepo' defined in rapid.repository.cases.accountTransaction.AccountTransactionRepo defined in @EnableJpaRepositories declared on CaseReaderApplication: Could not create query for public abstract java.util.List rapid.repository.cases.accountTransaction.AccountTransactionRepo.findByAccNumOrdered(java.lang.String); Reason: Validation failed for query for method public abstract java.util.List rapid.repository.cases.accountTransaction.AccountTransactionRepo.findByAccNumOrdered(java.lang.String)
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.initializeBean(AbstractAutowireCapableBeanFactory.java:1826) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.doCreateBean(AbstractAutowireCapableBeanFactory.java:607) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBean(AbstractAutowireCapableBeanFactory.java:529) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.AbstractBeanFactory.lambda$doGetBean$0(AbstractBeanFactory.java:339) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.DefaultSingletonBeanRegistry.getSingleton(DefaultSingletonBeanRegistry.java:373) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.AbstractBeanFactory.doGetBean(AbstractBeanFactory.java:337) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.AbstractBeanFactory.getBean(AbstractBeanFactory.java:202) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.config.DependencyDescriptor.resolveCandidate(DependencyDescriptor.java:254) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.DefaultListableBeanFactory.doResolveDependency(DefaultListableBeanFactory.java:1740) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.DefaultListableBeanFactory.resolveDependency(DefaultListableBeanFactory.java:1628) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.ConstructorResolver.resolveAutowiredArgument(ConstructorResolver.java:913) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.ConstructorResolver.createArgumentArray(ConstructorResolver.java:791) ~[spring-beans-6.2.7.jar:6.2.7]
        ... 19 common frames omitted
Caused by: org.springframework.data.repository.query.QueryCreationException: Could not create query for public abstract java.util.List rapid.repository.cases.accountTransaction.AccountTransactionRepo.findByAccNumOrdered(java.lang.String); Reason: Validation failed for query for method public abstract java.util.List rapid.repository.cases.accountTransaction.AccountTransactionRepo.findByAccNumOrdered(java.lang.String)
        at org.springframework.data.repository.query.QueryCreationException.create(QueryCreationException.java:101) ~[spring-data-commons-3.5.0.jar:3.5.0]
        at org.springframework.data.repository.core.support.QueryExecutorMethodInterceptor.lookupQuery(QueryExecutorMethodInterceptor.java:120) ~[spring-data-commons-3.5.0.jar:3.5.0]
        at org.springframework.data.repository.core.support.QueryExecutorMethodInterceptor.mapMethodsToQuery(QueryExecutorMethodInterceptor.java:104) ~[spring-data-commons-3.5.0.jar:3.5.0]
        at org.springframework.data.repository.core.support.QueryExecutorMethodInterceptor.lambda$new$0(QueryExecutorMethodInterceptor.java:92) ~[spring-data-commons-3.5.0.jar:3.5.0]
        at java.base/java.util.Optional.map(Optional.java:260) ~[na:na]
        at org.springframework.data.repository.core.support.QueryExecutorMethodInterceptor.<init>(QueryExecutorMethodInterceptor.java:92) ~[spring-data-commons-3.5.0.jar:3.5.0]
        at org.springframework.data.repository.core.support.RepositoryFactorySupport.getRepository(RepositoryFactorySupport.java:434) ~[spring-data-commons-3.5.0.jar:3.5.0]
        at org.springframework.data.repository.core.support.RepositoryFactoryBeanSupport.lambda$afterPropertiesSet$4(RepositoryFactoryBeanSupport.java:350) ~[spring-data-commons-3.5.0.jar:3.5.0]
        at org.springframework.data.util.Lazy.getNullable(Lazy.java:135) ~[spring-data-commons-3.5.0.jar:3.5.0]
        at org.springframework.data.util.Lazy.get(Lazy.java:113) ~[spring-data-commons-3.5.0.jar:3.5.0]
        at org.springframework.data.repository.core.support.RepositoryFactoryBeanSupport.afterPropertiesSet(RepositoryFactoryBeanSupport.java:356) ~[spring-data-commons-3.5.0.jar:3.5.0]
        at org.springframework.data.jpa.repository.support.JpaRepositoryFactoryBean.afterPropertiesSet(JpaRepositoryFactoryBean.java:132) ~[spring-data-jpa-3.5.0.jar:3.5.0]
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.invokeInitMethods(AbstractAutowireCapableBeanFactory.java:1873) ~[spring-beans-6.2.7.jar:6.2.7]
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.initializeBean(AbstractAutowireCapableBeanFactory.java:1822) ~[spring-beans-6.2.7.jar:6.2.7]
        ... 30 common frames omitted
Caused by: java.lang.IllegalArgumentException: Validation failed for query for method public abstract java.util.List rapid.repository.cases.accountTransaction.AccountTransactionRepo.findByAccNumOrdered(java.lang.String)
        at org.springframework.data.jpa.repository.query.SimpleJpaQuery.validateQuery(SimpleJpaQuery.java:97) ~[spring-data-jpa-3.5.0.jar:3.5.0]
        at org.springframework.data.jpa.repository.query.SimpleJpaQuery.<init>(SimpleJpaQuery.java:67) ~[spring-data-jpa-3.5.0.jar:3.5.0]
        at org.springframework.data.jpa.repository.query.JpaQueryFactory.fromMethodWithQueryString(JpaQueryFactory.java:49) ~[spring-data-jpa-3.5.0.jar:3.5.0]
        at org.springframework.data.jpa.repository.query.JpaQueryLookupStrategy$DeclaredQueryLookupStrategy.resolveQuery(JpaQueryLookupStrategy.java:174) ~[spring-data-jpa-3.5.0.jar:3.5.0]
        at org.springframework.data.jpa.repository.query.JpaQueryLookupStrategy$CreateIfNotFoundQueryLookupStrategy.resolveQuery(JpaQueryLookupStrategy.java:254) ~[spring-data-jpa-3.5.0.jar:3.5.0]
        at org.springframework.data.jpa.repository.query.JpaQueryLookupStrategy$AbstractQueryLookupStrategy.resolveQuery(JpaQueryLookupStrategy.java:99) ~[spring-data-jpa-3.5.0.jar:3.5.0]
        at org.springframework.data.repository.core.support.QueryExecutorMethodInterceptor.lookupQuery(QueryExecutorMethodInterceptor.java:116) ~[spring-data-commons-3.5.0.jar:3.5.0]
        ... 42 common frames omitted
Caused by: java.lang.IllegalArgumentException: org.hibernate.query.sqm.UnknownPathException: Could not resolve attribute 'accNum' of 'rapid.model.cases.accountTransaction.AccountTransaction' [SELECT a FROM AccountTransaction a WHERE a.accNum = :accNum ORDER BY a.caseNumber DESC, a.transNo DESC]
        at org.hibernate.internal.ExceptionConverterImpl.convert(ExceptionConverterImpl.java:143) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.internal.ExceptionConverterImpl.convert(ExceptionConverterImpl.java:167) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.internal.ExceptionConverterImpl.convert(ExceptionConverterImpl.java:173) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.internal.AbstractSharedSessionContract.createQuery(AbstractSharedSessionContract.java:886) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.internal.AbstractSharedSessionContract.createQuery(AbstractSharedSessionContract.java:796) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.internal.AbstractSharedSessionContract.createQuery(AbstractSharedSessionContract.java:143) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at java.base/jdk.internal.reflect.DirectMethodHandleAccessor.invoke(DirectMethodHandleAccessor.java:104) ~[na:na]
        at java.base/java.lang.reflect.Method.invoke(Method.java:565) ~[na:na]
        at org.springframework.orm.jpa.ExtendedEntityManagerCreator$ExtendedEntityManagerInvocationHandler.invoke(ExtendedEntityManagerCreator.java:364) ~[spring-orm-6.2.7.jar:6.2.7]
        at jdk.proxy2/jdk.proxy2.$Proxy162.createQuery(Unknown Source) ~[na:na]
        at org.springframework.data.jpa.repository.query.SimpleJpaQuery.validateQuery(SimpleJpaQuery.java:91) ~[spring-data-jpa-3.5.0.jar:3.5.0]
        ... 48 common frames omitted
Caused by: org.hibernate.query.sqm.UnknownPathException: Could not resolve attribute 'accNum' of 'rapid.model.cases.accountTransaction.AccountTransaction' [SELECT a FROM AccountTransaction a WHERE a.accNum = :accNum ORDER BY a.caseNumber DESC, a.transNo DESC]
        at org.hibernate.query.hql.internal.StandardHqlTranslator.translate(StandardHqlTranslator.java:88) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.query.internal.QueryInterpretationCacheStandardImpl.createHqlInterpretation(QueryInterpretationCacheStandardImpl.java:145) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.query.internal.QueryInterpretationCacheStandardImpl.resolveHqlInterpretation(QueryInterpretationCacheStandardImpl.java:132) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.query.spi.QueryEngine.interpretHql(QueryEngine.java:54) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.internal.AbstractSharedSessionContract.interpretHql(AbstractSharedSessionContract.java:832) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.internal.AbstractSharedSessionContract.createQuery(AbstractSharedSessionContract.java:878) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        ... 55 common frames omitted
Caused by: org.hibernate.query.sqm.PathElementException: Could not resolve attribute 'accNum' of 'rapid.model.cases.accountTransaction.AccountTransaction'
        at org.hibernate.query.sqm.SqmPathSource.getSubPathSource(SqmPathSource.java:95) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.query.sqm.tree.domain.AbstractSqmPath.get(AbstractSqmPath.java:198) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.query.sqm.tree.domain.AbstractSqmFrom.resolvePathPart(AbstractSqmFrom.java:198) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.query.hql.internal.DomainPathPart.resolvePathPart(DomainPathPart.java:42) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.query.hql.internal.BasicDotIdentifierConsumer.consumeIdentifier(BasicDotIdentifierConsumer.java:92) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.query.hql.internal.SemanticQueryBuilder.visitSimplePath(SemanticQueryBuilder.java:5465) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.query.hql.internal.SemanticQueryBuilder.visitGeneralPathFragment(SemanticQueryBuilder.java:5298) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.query.hql.internal.SemanticQueryBuilder.visitGeneralPathExpression(SemanticQueryBuilder.java:1891) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.grammars.hql.HqlParser$GeneralPathExpressionContext.accept(HqlParser.java:8268) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.antlr.v4.runtime.tree.AbstractParseTreeVisitor.visitChildren(AbstractParseTreeVisitor.java:46) ~[antlr4-runtime-4.13.0.jar:4.13.0]
        at org.hibernate.grammars.hql.HqlParserBaseVisitor.visitBarePrimaryExpression(HqlParserBaseVisitor.java:812) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.grammars.hql.HqlParser$BarePrimaryExpressionContext.accept(HqlParser.java:7726) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.query.hql.internal.SemanticQueryBuilder.createComparisonPredicate(SemanticQueryBuilder.java:2548) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.query.hql.internal.SemanticQueryBuilder.visitComparisonPredicate(SemanticQueryBuilder.java:2509) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.query.hql.internal.SemanticQueryBuilder.visitComparisonPredicate(SemanticQueryBuilder.java:277) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.grammars.hql.HqlParser$ComparisonPredicateContext.accept(HqlParser.java:6611) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.query.hql.internal.SemanticQueryBuilder.visitWhereClause(SemanticQueryBuilder.java:2361) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.query.hql.internal.SemanticQueryBuilder.visitWhereClause(SemanticQueryBuilder.java:277) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.grammars.hql.HqlParser$WhereClauseContext.accept(HqlParser.java:6302) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.query.hql.internal.SemanticQueryBuilder.visitQuery(SemanticQueryBuilder.java:1259) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.query.hql.internal.SemanticQueryBuilder.visitQuerySpecExpression(SemanticQueryBuilder.java:1040) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.query.hql.internal.SemanticQueryBuilder.visitQuerySpecExpression(SemanticQueryBuilder.java:277) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.grammars.hql.HqlParser$QuerySpecExpressionContext.accept(HqlParser.java:2134) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.query.hql.internal.SemanticQueryBuilder.visitSimpleQueryGroup(SemanticQueryBuilder.java:1025) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.query.hql.internal.SemanticQueryBuilder.visitSimpleQueryGroup(SemanticQueryBuilder.java:277) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.grammars.hql.HqlParser$SimpleQueryGroupContext.accept(HqlParser.java:2005) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.query.hql.internal.SemanticQueryBuilder.visitSelectStatement(SemanticQueryBuilder.java:492) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.query.hql.internal.SemanticQueryBuilder.visitStatement(SemanticQueryBuilder.java:451) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.query.hql.internal.SemanticQueryBuilder.buildSemanticModel(SemanticQueryBuilder.java:324) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        at org.hibernate.query.hql.internal.StandardHqlTranslator.translate(StandardHqlTranslator.java:71) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
        ... 60 common frames omitted

[INFO] ------------------------------------------------------------------------
[INFO] BUILD FAILURE
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  49.723 s
[INFO] Finished at: 2025-08-28T18:33:12-05:00
[INFO] ------------------------------------------------------------------------
[ERROR] Failed to execute goal org.springframework.boot:spring-boot-maven-plugin:3.4.2:run (default-cli) on project case-reader: Process terminated with exit code: 1 -> [Help 1]
[ERROR]
[ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
[ERROR] Re-run Maven using the -X switch to enable full debug logging.
[ERROR]
[ERROR] For more information about the errors and possible solutions, please read the following articles:
[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/MojoExecutionException
