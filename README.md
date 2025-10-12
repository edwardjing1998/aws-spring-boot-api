package rapid.client.web;

import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;
import rapid.dto.client.ClientEmailDTO;
import rapid.model.client.Client;
import rapid.service.client.ClientEmailService;
import rapid.exception.client.ClientBadRequestException;

import java.util.List;
import java.util.regex.Pattern;
import rapid.exception.client.ClientNotFoundException;
import rapid.exception.client.ClientEmailNotFoundException;
import rapid.service.client.ClientService;

@RestController
@RequestMapping("/api/")
@RequiredArgsConstructor
@Slf4j
public class ClientEmailWriterController {

    private final ClientEmailService clientEmailService;
    private final ClientService clientService;


    private static final Pattern CLINT_ID_PATTERN = Pattern.compile("^[0-9]{4}$");

    private static final Pattern EMAIL_PATTERN = Pattern.compile(
            "^[A-Za-z0-9+_.-]+@[A-Za-z0-9.-]+\\.[A-Za-z]{2,}$"
    );

    @PostMapping("email/add")
    public ResponseEntity<?> addClientEmail(@RequestBody ClientEmailDTO emailDTO) {

        final String rawClientId = emailDTO.getClientId();
        final String clientId = rawClientId == null ? "" : rawClientId.trim();
        final String emailRaw = emailDTO.getEmailAddressTx();
        final String email = emailRaw == null ? "" : emailRaw.trim();

        log.info("the client id {} requested for add email", clientId);

        if (!CLINT_ID_PATTERN.matcher(clientId).matches()) {
            log.info("the client id added {} is invalid", clientId);
            throw new ClientBadRequestException(
                    "Invalid 'clientNumber': must be exactly 4 digits and can not contains alphabetic characters"
            );
        }

        if (clientService.getAllClientsById(clientId).isEmpty()) {
            throw new ClientNotFoundException("Client not found: " + clientId);
        }

        if (!EMAIL_PATTERN.matcher(email).matches()) {
            throw new ClientBadRequestException("Invalid email address to add: " + email);
        }

        return ResponseEntity.of(clientEmailService.saveClientEmailFromDTO(emailDTO));
    }

    @PutMapping("email/update")
    public ResponseEntity<?> updateClientEmail(@RequestBody ClientEmailDTO emailDTO) {

        log.info("the client id updated {} requested for update email ", emailDTO.getClientId());

        if (!CLINT_ID_PATTERN.matcher(emailDTO.getClientId().trim()).matches()) {
            log.info("the client id update {} is invalid ", emailDTO.getClientId());
            throw new ClientBadRequestException("Invalid 'clientNumber': must be exactly 4 digits and can not contains alphabetic characters");
        }

        String email = emailDTO.getEmailAddressTx();

        if (email == null || !EMAIL_PATTERN.matcher(email.trim()).matches()) {
            throw new ClientBadRequestException("Invalid email address to update: " + email);
        }

       boolean clientEmailExists = clientEmailService.clientEmailExists(emailDTO.getClientId(), emailDTO.getEmailAddressTx());

        if(!clientEmailExists) {
            throw new ClientNotFoundException("Client not found: " + emailDTO.getClientId());
        }

        return ResponseEntity.of(clientEmailService.saveClientEmailFromDTO(emailDTO));
    }

    @DeleteMapping("email/delete")
    public ResponseEntity<String> deleteEmail(
            @RequestParam String clientId,
            @RequestParam String emailAddress
    ) {
        // align with /email/add validations
        String cid = (clientId == null) ? null : clientId.trim();
        if (cid == null || !CLINT_ID_PATTERN.matcher(cid).matches()) {
            throw new ClientBadRequestException(
                    "Invalid 'clientNumber': must be exactly 4 digits and can not contains alphabetic characters"
            );
        }

        String email = (emailAddress == null) ? null : emailAddress.trim();
        if (email == null || !EMAIL_PATTERN.matcher(email).matches()) {
            throw new ClientBadRequestException("Invalid email address: " + emailAddress);
        }

        // 1. check if client exists
        List<Client> clients = clientService.clientIsExist(cid);
        log.info("client exists {} ", clients.size());
        if (clients.isEmpty()) {
            throw new ClientNotFoundException("Client not found: " + cid);
        }

        // 2. check if email exists under that client
        boolean emailExists = clientEmailService.clientEmailExists(cid, email); // another helper in service
        if (!emailExists) {
            throw new ClientEmailNotFoundException(
                    "Email not found for clientId " + cid,  email
            );
        }

        // 3. delete email
        clientEmailService.deleteClientEmailById(cid, email);
        return ResponseEntity.ok("Email deleted successfully.");
    }

}
