2025-10-19T12:29:08.519-05:00  INFO 21800 --- [client-sysprin-writer] [0.0-8085-exec-1] o.s.web.servlet.DispatcherServlet        : Completed initialization in 6 ms
2025-10-19T12:29:13.107-05:00  INFO 21800 --- [client-sysprin-writer] [0.0-8085-exec-1] o.springdoc.api.AbstractOpenApiResource  : Init duration for springdoc-openapi is: 4310 ms
Hibernate: SELECT TOP 1
  CUST_TYPE, UNDELIVERABLE, STAT_A, STAT_B, STAT_C, STAT_D, STAT_E, STAT_F,
  STAT_I, STAT_L, STAT_O, STAT_U, STAT_X, STAT_Z,
  PO_BOX, ADDR_FLAG, TEMP_AWAY, RPS, SESSION, BAD_STATE, A_STAT_RCH, NM_13,
  TEMP_AWAY_ATTS, REPORT_METHOD, ACTIVE, NOTES, RET_STAT, DES_STAT, NON_US,
  SPECIAL, PIN, FORWARDING_ADDR, HOLD_DAYS, CONTACT, PHONE, ENTITY_CD
FROM sys_prins
WHERE client = ? AND sys_prin = ?

Hibernate: SELECT DISTINCT sys_prin
FROM sys_prins
WHERE client = ?

2025-10-19T12:31:01.206-05:00 ERROR 21800 --- [client-sysprin-writer] [0.0-8085-exec-3] o.a.c.c.C.[.[.[/].[dispatcherServlet]    : Servlet.service() for servlet [dispatcherServlet] in context with path [] threw exception [Request processing failed: java.lang.UnsupportedOperationException: Cannot project java.lang.Boolean to java.lang.Short; Target type is not an interface and no matching Converter found] with root cause

java.lang.UnsupportedOperationException: Cannot project java.lang.Boolean to java.lang.Short; Target type is not an interface and no matching Converter found
        at org.springframework.data.projection.ProjectingMethodInterceptor.potentiallyConvertResult(ProjectingMethodInterceptor.java:114) ~[spring-data-commons-3.5.0.jar:3.5.0]
        at org.springframework.data.projection.ProjectingMethodInterceptor.invoke(ProjectingMethodInterceptor.java:84) ~[spring-data-commons-3.5.0.jar:3.5.0]
        at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:184) ~[spring-aop-6.2.7.jar:6.2.7]
        at org.springframework.data.projection.ProxyProjectionFactory$TargetAwareMethodInterceptor.invoke(ProxyProjectionFactory.java:253) ~[spring-data-commons-3.5.0.jar:3.5.0]
        at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:184) ~[spring-aop-6.2.7.jar:6.2.7]
        at org.springframework.aop.framework.JdkDynamicAopProxy.invoke(JdkDynamicAopProxy.java:223) ~[spring-aop-6.2.7.jar:6.2.7]
        at jdk.proxy2/jdk.proxy2.$Proxy209.getACTIVE(Unknown Source) ~[na:na]
        at rapid.service.sysprin.SysPrinChangeAllService.copyAllFromTemplate(SysPrinChangeAllService.java:53) ~[common-services-0.0.1-SNAPSHOT.jar:na]
        at java.base/jdk.internal.reflect.DirectMethodHandleAccessor.invoke(DirectMethodHandleAccessor.java:104) ~[na:na]
        at java.base/java.lang.reflect.Method.invoke(Method.java:565) ~[na:na]
        at org.springframework.aop.support.AopUtils.invokeJoinpointUsingReflection(AopUtils.java:359) ~[spring-aop-6.2.7.jar:6.2.7]
        at org.springframework.aop.framework.ReflectiveMethodInvocation.invokeJoinpoint(ReflectiveMethodInvocation.java:196) ~[spring-aop-6.2.7.jar:6.2.7]
        at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:163) ~[spring-aop-6.2.7.jar:6.2.7]
        at org.springframework.transaction.interceptor.TransactionAspectSupport.invokeWithinTransaction(TransactionAspectSupport.java:380) ~[spring-tx-6.2.7.jar:6.2.7]
        at org.springframework.transaction.interceptor.TransactionInterceptor.invoke(TransactionInterceptor.java:119) ~[spring-tx-6.2.7.jar:6.2.7]
        at org.sprin
