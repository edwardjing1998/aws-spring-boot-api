ervice   : Stopping service [Tomcat]
2025-11-24T13:56:12.332-06:00  INFO 35484 --- [client-sysprin-writer] [           main] .s.b.a.l.ConditionEvaluationReportLogger :

Error starting ApplicationContext. To display the condition evaluation report re-run your application with 'debug' enabled.
2025-11-24T13:56:12.368-06:00 ERROR 35484 --- [client-sysprin-writer] [           main] o.s.b.d.LoggingFailureAnalysisReporter   :

***************************
APPLICATION FAILED TO START
***************************

Description:

Parameter 2 of constructor in rapid.service.searchintegration.SearchClientService required a bean of type 'org.springframework.web.client.RestTemplate' that could not be found.



package rapid.service.searchintegration;

import jakarta.annotation.PostConstruct;
import lombok.AllArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Service;
import org.springframework.web.client.RestTemplate;
import rapid.dto.client.ClientDTO;
import rapid.dto.client.ClientSearchDTO;
import rapid.model.client.Client;
import rapid.repository.client.ClientRepository;

import java.util.List;
import java.util.stream.Collectors;

@Service
@AllArgsConstructor
@Slf4j
public class SearchClientService {

    private final ClientRepository clientRepository;
    private final ClientIndexer luceneIndexer;
    private final RestTemplate restTemplate;

    @PostConstruct
    public void initLucene() throws Exception {
        // full index on startup
        luceneIndexer.indexClients(clientRepository.findAllValidClients());
    }

    // 🔍 search API used by controller
    public List<ClientSearchDTO> getClientSearch(String keyword) throws Exception {
        return luceneIndexer.searchClients(keyword).stream()
                .map(c -> new ClientSearchDTO(c.getClient(), c.getName()))
                .collect(Collectors.toList());
    }

    // ✅ optional: rebuild everything (e.g. admin endpoint)
    public void reindexAll() throws Exception {
        luceneIndexer.indexClients(clientRepository.findAllValidClients());
    }

    // ✅ incremental update for one client (call after create or update)
    public void indexClient(Client client) {
        try {
            luceneIndexer.indexClient(client);
        } catch (Exception e) {
            // you can choose to log only, or rethrow
            throw new RuntimeException("Failed to index client " + client.getClient(), e);
        }
    }

    public void indexClientAPI(ClientDTO dto) {
        try {
            String url = "http://localhost:8089/search-integration/api/client-index";
            restTemplate.postForEntity(url, dto, Void.class);
        } catch (Exception ex) {
            log.warn("Failed to update search index for client {}", dto.getClient(), ex);
        }
    }

    // ✅ remove client from index (call after delete)
    public void deleteClientFromIndex(String clientCode) {
        try {
            luceneIndexer.deleteClient(clientCode);
        } catch (Exception e) {
            throw new RuntimeException("Failed to delete client from index: " + clientCode, e);
        }
    }
}
