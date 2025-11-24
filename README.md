package rapid.searchintegration.service;

import jakarta.annotation.PostConstruct;
import lombok.AllArgsConstructor;
import org.springframework.stereotype.Service;
import rapid.dto.client.ClientSearchDTO;
import rapid.model.client.Client;
import rapid.repository.client.ClientRepository;

import java.util.List;
import java.util.stream.Collectors;

@Service
@AllArgsConstructor
public class SearchClientService {

    private final ClientRepository clientRepository;
    private final ClientIndexer luceneIndexer;

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

    // ✅ remove client from index (call after delete)
    public void deleteClientFromIndex(String clientCode) {
        try {
            luceneIndexer.deleteClient(clientCode);
        } catch (Exception e) {
            throw new RuntimeException("Failed to delete client from index: " + clientCode, e);
        }
    }
}
