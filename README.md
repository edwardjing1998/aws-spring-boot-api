package rapid.searchintegration.web;

import lombok.AllArgsConstructor;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;
import rapid.dto.client.ClientSearchDTO;
import rapid.service.searchintegration.SearchClientService;

import java.util.List;

@RestController
@RequestMapping("/api")
@AllArgsConstructor
public class SearchClientController {

    private final SearchClientService clientService;

    @GetMapping("/client-autocomplete")
    public ResponseEntity<List<ClientSearchDTO>> autocomplete(@RequestParam String keyword) {
        try {
            List<ClientSearchDTO> results = clientService.getClientSearch(keyword);
            if (results == null || results.isEmpty()) {
                return ResponseEntity.ok(
                        List.of(new ClientSearchDTO(keyword, "client not found"))
                );
            }
            return ResponseEntity.ok(results);
        } catch (Exception e) {
            return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR)
                    .body(List.of(new ClientSearchDTO("error", e.getMessage())));
        }
    }
}
