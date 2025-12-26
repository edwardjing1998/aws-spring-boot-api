package rapid.client.web;

import jakarta.validation.constraints.Min;
import jakarta.validation.constraints.Size;
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.apache.commons.lang3.StringUtils;
import org.springframework.data.domain.Page;
import org.springframework.http.HttpStatus;
import org.springframework.http.MediaType;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;
import org.springframework.web.server.ResponseStatusException;
import rapid.dto.client.ClientDTO;
import rapid.exception.client.ClientBadRequestException;
import rapid.exception.client.ClientNotFoundException;
import rapid.model.client.Client;
import rapid.service.client.ClientNativeQueryService;
import rapid.service.client.ClientService;

import java.util.List;

@RestController
@RequestMapping("/api/")
@RequiredArgsConstructor
@Slf4j
public class ClientController {

    private final ClientService clientService;
    private final ClientNativeQueryService clientNativeSqlService;

    @GetMapping("client/legacy/{clientId}")
    public ResponseEntity<List<ClientDTO>> getClientByClientId(@PathVariable @Size(max = 4, message = "ClientId should not be more than 4 characters") String clientId) {

        if (StringUtils.isBlank(clientId)) {
            throw new ClientBadRequestException(
                    "clientId can not be empty");
        }

        List<ClientDTO> clientDTOS = clientService.getAllClientsById(clientId);

        if (clientDTOS.isEmpty()) {
            throw new ClientNotFoundException("Client not found: " + clientId);
        }

        return ResponseEntity.ok(clientDTOS);
    }

    @GetMapping(value = "/client/details", produces = MediaType.APPLICATION_JSON_VALUE)
    public ResponseEntity<String> getClientsJson(
            @RequestParam(defaultValue = "0") @Min(0) int page,
            @RequestParam(defaultValue = "20") @Min(1) int size) {

        log.info("request client details for page {} size {}", page, size);

        String json = clientNativeSqlService.fetchClientDetailJsonPage(page, size);
        if (json == null || json.equalsIgnoreCase("[]")) {
            throw new ResponseStatusException(
                    HttpStatus.NOT_FOUND,
                    "records not found:"
            );
        }

        // Log just length to avoid dumping huge payloads
        log.info("/client/details page={}, size={}, bytes={}", page, size, json.length());

        return ResponseEntity
                .ok()
                .contentType(MediaType.APPLICATION_JSON)
                .body(json);
    }

    @GetMapping(value = "/client/{clientId}", produces = MediaType.APPLICATION_JSON_VALUE)
    public ResponseEntity<String> getClientJsonByClientId(@PathVariable @Size(max = 4, message = "ClientId should not be more than 4 characters") String clientId,
                                                          @RequestParam(defaultValue = "0") @Min(0) int page,
                                                          @RequestParam(defaultValue = "20") @Min(1) int size) {

        log.info("request client details by clientId {}", clientId);

        String json = clientNativeSqlService.fetchClientDetailJsonByClientIdPage(clientId, page, size);
        if (json == null || json.equalsIgnoreCase("[]")) {
            throw new ResponseStatusException(
                    HttpStatus.NOT_FOUND,
                    "records not found:"
            );
        }

        // Log just length to avoid dumping huge payloads
        log.info("/client/details clientId={}, size={}", clientId, json.length());

        return ResponseEntity
                .ok()
                .contentType(MediaType.APPLICATION_JSON)
                .body(json);
    }

    @GetMapping(value = "/clients/detail", produces = MediaType.APPLICATION_JSON_VALUE)
    public ResponseEntity<Page<Client>> getClientsDetail(
            @RequestParam(defaultValue = "0") @Min(0) int page,
            @RequestParam(defaultValue = "20") @Min(1) int size) {

        log.info("requesting client details for page {} size {}", page, size);

        Page<Client> clients = clientService.getClientsWithChildren(page, size);
        if (clients.isEmpty()) {
            throw new ResponseStatusException(
                    HttpStatus.NOT_FOUND,
                    "records not found:"
            );
        }

        // Log just length to avoid dumping huge payloads
        log.info("/clients/detail page={}, size={}, bytes={}", page, size, clients.toList().size());

        return ResponseEntity
                .ok()
                .contentType(MediaType.APPLICATION_JSON)
                .body(clients);
    }
}







[ERROR] Failed to execute goal org.apache.maven.plugins:maven-compiler-plugin:3.13.0:compile (default-compile) on project client-sysprin-reader: Compilation failure: Compilation failure:
[ERROR] /C:/Users/F2LIPBX/spring_boot/harish/trace-clients/client-sysprin-reader/src/main/java/rapid/client/web/ClientController.java:[18,28] cannot find symbol
[ERROR]   symbol:   class ClientNativeQueryService
[ERROR]   location: package rapid.service.client
[ERROR] /C:/Users/F2LIPBX/spring_boot/harish/trace-clients/client-sysprin-reader/src/main/java/rapid/client/web/ClientController.java:[30,19] cannot find symbol
[ERROR]   symbol:   class ClientNativeQueryService
[ERROR]   location: class rapid.client.web.ClientController
[ERROR] /C:/Users/F2LIPBX/spring_boot/harish/trace-clients/client-sysprin-reader/src/main/java/rapid/client/web/ClientController.java:[25,1] cannot find symbol
[ERROR]   symbol:   class ClientNativeQueryService
[ERROR]   location: class rapid.client.web.ClientController
[ERROR] -> [Help 1]
[ERROR]
[ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
[ERROR] Re-run Maven using the -X switch to enable full debug logging.
[ERROR]
[ERROR] For more information about the errors and possible solutions, please read the following articles:
[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/MojoFailureException
[ERROR]
