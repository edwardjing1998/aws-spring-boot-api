<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-webflux</artifactId>
</dependency>
<dependency>
  <groupId>io.projectreactor.netty</groupId>
  <artifactId>reactor-netty</artifactId>
</dependency>



@Configuration
public class WebClientConfig {
  @Bean
  public WebClient clientWebClient(@Value("${client.base-url:http://localhost:8080}") String baseUrl) {
    return WebClient.builder().baseUrl(baseUrl).build();
  }
}



Hibernate: insert into deleted_labels (bar_code_tx,card_type_tx,CASE_NUMBER,status,text1,text2,text3,text3_addr3,text3_addr4,text4,text5,type) values (?,?,?,?,?,?,?,?,?,?,?,?)
Hibernate: insert into deleted_labels (bar_code_tx,card_type_tx,CASE_NUMBER,status,text1,text2,text3,text3_addr3,text3_addr4,text4,text5,type) values (?,?,?,?,?,?,?,?,?,?,?,?)
Hibernate: insert into deleted_transactions (command_line,cycle,retry_count,system_type,type,CASE_NUMBER,date_time,trans_no) values (?,?,?,?,?,?,?,?)
Hibernate: insert into deleted_transactions (command_line,cycle,retry_count,system_type,type,CASE_NUMBER,date_time,trans_no) values (?,?,?,?,?,?,?,?)
2025-08-29T12:07:17.904-05:00  WARN 25292 --- [case-reader] [           main] ConfigServletWebServerApplicationContext : Exception encountered during context initialization - cancelling refresh attempt: org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating bean with name 'caseReaderController' defined in file [C:\Users\F2LIPBX\spring_boot\kalpana\trace-cases\case-reader\target\classes\rapid\cases\web\CaseReaderController.class]: Unsatisfied dependency expressed through constructor parameter 1: No qualifying bean of type 'rapid.cases.service.GetCaseNumberService' available: expected at least 1 bean which qualifies as autowire candidate. Dependency annotations: {}
2025-08-29T12:07:17.905-05:00  INFO 25292 --- [case-reader] [           main] j.LocalContainerEntityManagerFactoryBean : Closing JPA EntityManagerFactory for persistence unit 'default'
2025-08-29T12:07:17.918-05:00  INFO 25292 --- [case-reader] [           main] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Shutdown initiated...
2025-08-29T12:07:17.924-05:00  INFO 25292 --- [case-reader] [           main] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Shutdown completed.
2025-08-29T12:07:17.937-05:00  INFO 25292 --- [case-reader] [           main] o.apache.catalina.core.StandardService   : Stopping service [Tomcat]
2025-08-29T12:07:17.984-05:00  INFO 25292 --- [case-reader] [           main] .s.b.a.l.ConditionEvaluationReportLogger :

Error starting ApplicationContext. To display the condition evaluation report re-run your application with 'debug' enabled.
2025-08-29T12:07:18.061-05:00 ERROR 25292 --- [case-reader] [           main] o.s.b.d.LoggingFailureAnalysisReporter   :

***************************
APPLICATION FAILED TO START
***************************

Description:

Parameter 1 of constructor in rapid.cases.web.CaseReaderController required a bean of type 'rapid.cases.service.GetCaseNumberService' that could not be found.






package rapid.cases.service;

import lombok.AllArgsConstructor;
import org.springframework.stereotype.Service;
import rapid.cases.config.StoredProcExecutor;

import java.util.List;
import java.util.Map;

@AllArgsConstructor
@Service
public class GetTransactionNumberService {
    private final StoredProcExecutor storedProcExecutor;
    public String getTransactionNumber(String caseNo) {
        if (caseNo == null || caseNo.trim().isEmpty()) {
            throw new IllegalArgumentException("Case number is required to get transaction number.");
        }

        try {
            // Call the stored procedure with case number as input
            Map<String, Object> result = storedProcExecutor.executeStoredProc("usp_get_next_robot_transaction", List.of(caseNo));

            if (result.isEmpty() || result.get("tran_no") == null) {
                return "01"; // Default value if no result
            }

            String tranNo = result.get("tran_no").toString();
            return tranNo.length() >= 2 ? tranNo.substring(tranNo.length() - 2) : tranNo;

        } catch (Exception e) {
            // Log error or rethrow as needed
            throw new RuntimeException("Error retrieving transaction number", e);
        }
    }
}



package rapid.cases.service;


import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Service;
import rapid.cases.service.integration.ClientServiceIntegration;
import rapid.model.cases.cases.Case;
import rapid.repository.cases.cases.CaseRepo;

import java.util.List;
import java.util.Optional;

@Service
@RequiredArgsConstructor
@Slf4j
public class UserCaseService {
    private final CaseRepo caseRepo;
    private final ClientServiceIntegration clientServiceIntegration;

    public boolean checkForOpenCase(String account) {
        try {
            long count = caseRepo.countActiveCasesByPiId(account);
            return count > 0;
        } catch (Exception e) {

            return false;
        }
    }

    public Optional<Case> getExistingCase(String sAccount, String vCaseNo) {

        Optional<Case> accountDetails;
        if (vCaseNo == null) {
            accountDetails = caseRepo.findByPiIdAndActive(sAccount, true);
        } else {
            accountDetails = caseRepo.findByCaseNumber(vCaseNo);
        }
        return accountDetails;
    }

    public List<Case> getCasesWithBillingSp(String accNum) {
        List<Case> cases = caseRepo.findByPiIdOrderByCaseNumberDesc(accNum);
        for (Case c : cases) {
            String billingSp = clientServiceIntegration.getBillingSpBySysPrin(c.getSysPrin());
            System.out.println(billingSp);
            c.setBilling_sp(billingSp);
        }
        return cases;
    }
}



