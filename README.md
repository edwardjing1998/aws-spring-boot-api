13:17:29.395 [main] INFO org.springframework.mock.web.MockServletContext -- Initializing Spring TestDispatcherServlet ''
13:17:29.395 [main] INFO org.springframework.test.web.servlet.TestDispatcherServlet -- Initializing Servlet ''
13:17:29.396 [main] INFO org.springframework.test.web.servlet.TestDispatcherServlet -- Completed initialization in 1 ms
13:17:29.398 [main] INFO rapid.cases.web.CaseWriterController -- DELETE /cases/delete ΓÇô account number ACC123
[ERROR] Tests run: 5, Failures: 0, Errors: 1, Skipped: 0, Time elapsed: 5.174 s <<< FAILURE! -- in rapid.cases.web.CaseWriterControllerTest
[ERROR] rapid.cases.web.CaseWriterControllerTest.testUpdateCase_success -- Time elapsed: 0.106 s <<< ERROR!
jakarta.servlet.ServletException: Request processing failed: java.lang.IllegalStateException: Ambiguous handler methods mapped for '/case/CASE456': {public org.springframework.http
.ResponseEntity rapid.cases.web.CaseWriterController.updateCase(java.lang.String,rapid.dto.cases.CaseDTO), public org.springframework.http.ResponseEntity rapid.cases.web.CaseWriterController.updateUserCase(rapid.dto.cases.CaseDTO)}
        at org.springframework.web.servlet.FrameworkServlet.processRequest(FrameworkServlet.java:1022)
        at org.springframework.web.servlet.FrameworkServlet.doPut(FrameworkServlet.java:925)
        at jakarta.servlet.http.HttpServlet.service(HttpServlet.java:593)
        at org.springframework.web.servlet.FrameworkServlet.service(FrameworkServlet.java:885)
        at org.springframework.test.web.servlet.TestDispatcherServlet.service(TestDispatcherServlet.java:72)
        at jakarta.servlet.http.HttpServlet.service(HttpServlet.java:658)
        at org.springframework.mock.web.MockFilterChain$ServletFilterProxy.doFilter(MockFilterChain.java:165)
        at org.springframework.mock.web.MockFilterChain.doFilter(MockFilterChain.java:132)
        at org.springframework.test.web.servlet.MockMvc.perform(MockMvc.java:201)
        at rapid.cases.web.CaseWriterControllerTest.testUpdateCase_success(CaseWriterControllerTest.java:128)
        at java.base/java.lang.reflect.Method.invoke(Method.java:565)
        at java.base/java.util.ArrayList.forEach(ArrayList.java:1604)
        at java.base/java.util.ArrayList.forEach(ArrayList.java:1604)
Caused by: java.lang.IllegalStateException: Ambiguous handler methods mapped for '/case/CASE456': {public org.springframework.http.ResponseEntity rapid.cases.web.CaseWriterControll
er.updateCase(java.lang.String,rapid.dto.cases.CaseDTO), public org.springframework.http.ResponseEntity rapid.cases.web.CaseWriterController.updateUserCase(rapid.dto.cases.CaseDTO)}
        at org.springframework.web.servlet.handler.AbstractHandlerMethodMapping.lookupHandlerMethod(AbstractHandlerMethodMapping.java:431)
        at org.springframework.web.servlet.handler.AbstractHandlerMethodMapping.getHandlerInternal(AbstractHandlerMethodMapping.java:382)
        at org.springframework.web.servlet.mvc.method.RequestMappingInfoHandlerMapping.getHandlerInternal(RequestMappingInfoHandlerMapping.java:127)
        at org.springframework.web.servlet.mvc.method.RequestMappingInfoHandlerMapping.getHandlerInternal(RequestMappingInfoHandlerMapping.java:68)
        at org.springframework.web.servlet.handler.AbstractHandlerMapping.getHandler(AbstractHandlerMapping.java:509)
        at org.springframework.web.servlet.DispatcherServlet.getHandler(DispatcherServlet.java:1284)
        at org.springframework.test.web.servlet.TestDispatcherServlet.getHandler(TestDispatcherServlet.java:123)
        at org.springframework.web.servlet.DispatcherServlet.doDispatch(DispatcherServlet.java:1065)
        at org.springframework.web.servlet.DispatcherServlet.doService(DispatcherServlet.java:979)
        at org.springframework.web.servlet.FrameworkServlet.processRequest(FrameworkServlet.java:1014)
        ... 12 more

[INFO] 
[INFO] Results:
[INFO]
[ERROR] Errors: 
[ERROR]   CaseWriterControllerTest.testUpdateCase_success:128 ┬╗ Servlet Request processing failed: java.lang.IllegalStateException: Ambiguous handler methods mapped for '/case/CAS
E456': {public org.springframework.http.ResponseEntity rapid.cases.web.CaseWriterController.updateCase(java.lang.String,rapid.dto.cases.CaseDTO), public org.springframework.http.ResponseEntity rapid.cases.web.CaseWriterController.updateUserCase(rapid.dto.cases.CaseDTO)}                                                                                          
[INFO]
[ERROR] Tests run: 9, Failures: 0, Errors: 1, Skipped: 0
