Handler, 1 ResponseBodyAdvice
2025-07-06T11:24:41.660-05:00  INFO 27212 --- [client-sysprin-reader] [           main] o.s.b.a.h2.H2ConsoleAutoConfiguration    : H2 console available at '/h2-console'. Database available at 'jdbc:h2:mem:testdb'
2025-07-06T11:24:41.703-05:00  INFO 27212 --- [client-sysprin-reader] [           main] o.s.b.a.e.web.EndpointLinksResolver      : Exposing 1 endpoint beneath base path '/actuator'
2025-07-06T11:24:41.878-05:00  INFO 27212 --- [client-sysprin-reader] [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port 8083 (http) with context path '/'
2025-07-06T11:24:41.902-05:00  INFO 27212 --- [client-sysprin-reader] [           main] rapid.client.ClientReaderApplication     : Started ClientReaderApplication in 21.451 seconds (process running for 23.306)
2025-07-06T11:25:56.116-05:00  INFO 27212 --- [client-sysprin-reader] [0.0-8083-exec-3] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring DispatcherServlet 'dispatcherServlet'
2025-07-06T11:25:56.116-05:00  INFO 27212 --- [client-sysprin-reader] [0.0-8083-exec-3] o.s.web.servlet.DispatcherServlet        : Initializing Servlet 'dispatcherServlet'
2025-07-06T11:25:56.117-05:00 DEBUG 27212 --- [client-sysprin-reader] [0.0-8083-exec-3] o.s.web.servlet.DispatcherServlet        : Detected StandardServletMultipartResolver
2025-07-06T11:25:56.118-05:00 DEBUG 27212 --- [client-sysprin-reader] [0.0-8083-exec-3] o.s.web.servlet.DispatcherServlet        : Detected AcceptHeaderLocaleResolver
2025-07-06T11:25:56.119-05:00 DEBUG 27212 --- [client-sysprin-reader] [0.0-8083-exec-3] o.s.web.servlet.DispatcherServlet        : Detected FixedThemeResolver
2025-07-06T11:25:56.122-05:00 DEBUG 27212 --- [client-sysprin-reader] [0.0-8083-exec-3] o.s.web.servlet.DispatcherServlet        : Detected org.springframework.web.servlet.view.DefaultRequestToViewNameTranslator@3779b701
2025-07-06T11:25:56.122-05:00 DEBUG 27212 --- [client-sysprin-reader] [0.0-8083-exec-3] o.s.web.servlet.DispatcherServlet        : Detected org.springframework.web.servlet.support.SessionFlashMapManager@60eeefe
2025-07-06T11:25:56.123-05:00 DEBUG 27212 --- [client-sysprin-reader] [0.0-8083-exec-3] o.s.web.servlet.DispatcherServlet        : enableLoggingRequestDetails='false': request parameters and headers will be masked to prevent unsafe logging of potentially sensitive data
2025-07-06T11:25:56.123-05:00  INFO 27212 --- [client-sysprin-reader] [0.0-8083-exec-3] o.s.web.servlet.DispatcherServlet        : Completed initialization in 6 ms
2025-07-06T11:25:56.130-05:00 DEBUG 27212 --- [client-sysprin-reader] [0.0-8083-exec-3] o.s.web.servlet.DispatcherServlet        : GET "/client-sysprin-reader/api/clients/all", parameters={}
2025-07-06T11:25:56.144-05:00 DEBUG 27212 --- [client-sysprin-reader] [0.0-8083-exec-3] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped to ResourceHttpRequestHandler [classpath [META-INF/resources/], classpath [resources/], classpath [static/], classpath [public/], ServletContext [/]]
2025-07-06T11:25:56.164-05:00 DEBUG 27212 --- [client-sysprin-reader] [0.0-8083-exec-3] o.s.w.s.r.ResourceHttpRequestHandler     : Resource not found
2025-07-06T11:25:56.170-05:00 DEBUG 27212 --- [client-sysprin-reader] [0.0-8083-exec-3] .w.s.m.s.DefaultHandlerExceptionResolver : Resolved [org.springframework.web.servlet.resource.NoResourceFoundException: No static resource client-sysprin-reader/api/clients/all.]
2025-07-06T11:25:56.171-05:00 DEBUG 27212 --- [client-sysprin-reader] [0.0-8083-exec-3] o.s.web.servlet.DispatcherServlet        : Completed 404 NOT_FOUND
2025-07-06T11:25:56.179-05:00 DEBUG 27212 --- [client-sysprin-reader] [0.0-8083-exec-3] o.s.web.servlet.DispatcherServlet        : "ERROR" dispatch for GET "/error", parameters={}
2025-07-06T11:25:56.182-05:00 DEBUG 27212 --- [client-sysprin-reader] [0.0-8083-exec-3] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped to org.springframework.boot.autoconfigure.web.servlet.error.BasicErrorController#error(HttpServletRequest)
2025-07-06T11:25:56.220-05:00 DEBUG 27212 --- [client-sysprin-reader] [0.0-8083-exec-3] o.s.w.s.m.m.a.HttpEntityMethodProcessor  : Using 'application/json', given [*/*] and supported [application/json, application/*+json, application/yaml]
2025-07-06T11:25:56.222-05:00 DEBUG 27212 --- [client-sysprin-reader] [0.0-8083-exec-3] o.s.w.s.m.m.a.HttpEntityMethodProcessor  : Writing [{timestamp=Sun Jul 06 11:25:56 CDT 2025, status=404, error=Not Found, path=/client-sysprin-reader/ap (truncated)...]
2025-07-06T11:25:56.269-05:00 DEBUG 27212 --- [client-sysprin-reader] [0.0-8083-exec-3] o.s.web.servlet.DispatcherServlet        : Exiting from "ERROR" dispatch, status 404
