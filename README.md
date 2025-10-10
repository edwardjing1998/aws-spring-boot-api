025-10-10T18:54:10.080-05:00  INFO 33492 --- [client-sysprin-writer] [0.0-8085-exec-9] r.c.web.ClientEmailWriterController      : the client id null requested for add email
2025-10-10T18:54:10.207-05:00 ERROR 33492 --- [client-sysprin-writer] [0.0-8085-exec-9] o.a.c.c.C.[.[.[/].[dispatcherServlet]    : Servlet.service() for servlet [dispatcherServlet] in context with path [] threw exception [Request processing failed: java.lang.NullPointerException: Cannot invoke "String.trim()" because the return value of "rapid.dto.client.ClientEmailDTO.getClientId()" is null] with root cause

java.lang.NullPointerException: Cannot invoke "String.trim()" because the return value of "rapid.dto.client.ClientEmailDTO.getClientId()" is null
        at rapid.client.web.ClientEmailWriterController.addClientEmail(ClientEmailWriterController.java:39) ~[classes/:na]
        at java.base/jdk.internal.reflect.DirectMethodHandleAccessor.invoke(DirectMethodHandleAccessor.java:104) ~[na:na]
        at java.base/java.lang.reflect.Method.invoke(Method.java:565) ~[na:na]
        at org.springframework.web.method.support.InvocableHandlerMethod.doInvoke(InvocableHandlerMethod.java:258) ~[spring-web-6.2.7.jar:6.2.7]
        at org.springframework.web.method.support.InvocableHandlerMethod.invokeForRequest(InvocableHandlerMethod.java:191) ~[spring-web-6.2.7.jar:6.2.7]
        at org.springframework.web.servlet.mvc.method.annotation.ServletInvocableHandlerMethod.invokeAndHandle(ServletInvocableHandlerMethod.java:118) ~[spring-webmvc-6.2.7.jar:6.2.7]
        at org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter.invokeHandlerMethod(RequestMappingHandlerAdapter.java:986) ~[spring-webmvc-6.2.7.jar:6.2.7]
        at org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter.handleInternal(RequestMappingHandlerAdapter.java:891) ~[spring-webmvc-6.2.7.jar:6.2.7]
        at org.springframework.web.servlet.mvc.method.AbstractHandlerMethodAdapter.handle(AbstractHandlerMethodAdapter.java:87) ~[spring-webmvc-6.2.7.jar:6.2.7]
        at org.springframework.web.servlet.DispatcherServlet.doDispatch(DispatcherServlet.java:1089) ~[spring-webmvc-6.2.7.jar:6.2.7]
     
