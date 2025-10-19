Hibernate: SELECT DISTINCT sys_prin
FROM sys_prins
WHERE client = ?

2025-10-19T12:51:58.768-05:00 ERROR 18240 --- [client-sysprin-writer] [0.0-8085-exec-1] o.a.c.c.C.[.[.[/].[dispatcherServlet]    : Servlet.service() for servlet [dispatcherServlet] in context with path [] threw exception [Handler dispatch failed: java.lang.NoSuchMethodError: 'java.lang.Short rapid.repository.sysprin.SysPrinNativeRepository$TemplateRow.getACTIVE()'] with root cause

java.lang.NoSuchMethodError: 'java.lang.Short rapid.repository.sysprin.SysPrinNativeRepository$TemplateRow.getACTIVE()'
        at rapid.service.sysprin.SysPrinChangeAllService.copyAllFromTemplate(SysPrinChangeAllService.java:53) ~[common-services-0.0.1-SNAPSHOT.jar:na]
        at java.base/jdk.internal.reflect.DirectMethodHandleAccessor.invoke(DirectMethodHandleAccessor.java:104) ~[na:na]
        at java.base/java.lang.reflect.Method.invoke(Method.java:565) ~[na:na]
        at org.springframework.aop.support.AopUtils.invokeJoinpointUsingReflection(AopUtils.java:359) ~[spring-aop-6.2.7.jar:6.2.7]
        at o
