package rapid.integration.search;

import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;
import org.springframework.web.client.RestTemplate;
import rapid.dto.client.ClientSearchDTO;
import rapid.model.client.Client;

@Slf4j
@Component
@RequiredArgsConstructor
public class ClientIndexingClient {

    private final RestTemplate restTemplate;

    // 在 admin 的 application.yml 中配置，例如：
    // search-integration:
    //   base-url: http://localhost:8087  (假设 search-integration 跑在 8087)
    @Value("${search-integration.base-url}")
    private String searchIntegrationBaseUrl;

    public void indexClient(Client client) {
        try {
            ClientSearchDTO dto = new ClientSearchDTO(client.getClient(), client.getName());
            String url = searchIntegrationBaseUrl + "/api/client-index";
            restTemplate.postForEntity(url, dto, Void.class);
        } catch (Exception ex) {
            // 这里建议只打 log，不要让整个创建 client 事务回滚
            log.warn("Failed to update search index for client {}", client.getClient(), ex);
        }
    }
}
