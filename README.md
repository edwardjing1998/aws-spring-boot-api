package rapid.searchintegration.service;

import jakarta.annotation.PostConstruct;
import lombok.AllArgsConstructor;
import org.springframework.stereotype.Service;
import rapid.dto.client.ClientSearchDTO;
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
        luceneIndexer.indexClients(clientRepository.findAllValidClients());
    }

    public List<ClientSearchDTO> getClientSearch(String keyword) throws Exception {
        return luceneIndexer.searchClients(keyword).stream()
                .map(c -> new ClientSearchDTO(c.getClient(), c.getName()))
                .collect(Collectors.toList());
    }
}
