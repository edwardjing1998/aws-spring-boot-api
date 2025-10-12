package rapid.client.web;

import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;
import rapid.dto.client.ClientEmailDTO;
import rapid.exception.client.ClientBadRequestException;
import rapid.exception.client.ClientEmailNotFoundException;
import rapid.exception.client.ClientNotFoundException;
import rapid.model.client.Client;
import rapid.service.client.ClientEmailService;
import rapid.service.client.ClientService;

import java.util.List;
import java.util.Objects;
import java.util.regex.Pattern;

@RestController
@RequestMapping("/api")
@RequiredArgsConstructor
@Slf4j
public class ClientEmailWriterController {

    private final ClientEmailService clientEmailService;
    private final ClientService clientService;

    private static final Pattern CLINT_ID_PATTERN = Pattern.compile("^[0-9]{4}$");
    private static final Pattern EMAIL_PATTERN = Pattern.compile("^[A-Za-z0-9+_.-]+@[A-Za-z0-9.-]+\\.[A-Za-z]{2,}$");

    // ------------------------ Helpers ------------------------

    private static String nzTrim(String s) { return s == null ? "" : s.trim(); }

    private static void validateClientIdOrThrow(String clientId) {
        if (!CLINT_ID_PATTERN.matcher(clientId).matches()) {
            throw new ClientBadRequestException("Invalid 'clientNumber': must be exactly 4 digits and cannot contain alphabetic characters");
        }
    }

    private static void validateEmailOrThrow(String email) {
        if (!EMAIL_PATTERN.matcher(email).matches()) {
            throw new ClientBadRequestException("Invalid email address: " + email);
        }
    }

    private void ensureClientExistsOrThrow(String clientId) {
        // If you have a faster exists() in service/repo, prefer that.
        // Keeping your current API:
        List<Client> list = clientService.clientIsExist(clientId);
        if (list == null || list.isEmpty()) {
            throw new ClientNotFoundException("Client not found: " + clientId);
        }
    }

    // ------------------------ Endpoints ------------------------

    @PostMapping("/email/add")
    public ResponseEntity<?> addClientEmail(@RequestBody ClientEmailDTO emailDTO) {
        Objects.requireNonNull(emailDTO, "Body must not be null");

        final String clientId = nzTrim(emailDTO.getClientId());
        final String email    = nzTrim(emailDTO.getEmailAddressTx());

        log.info("Add email requested for clientId={}", clientId);

        validateClientIdOrThrow(clientId);
        ensureClientExistsOrThrow(clientId);
        validateEmailOrThrow(email);

        // Save and return 200 with body if present, 404 if empty (no extra orElseThrow at tail)
        return ResponseEntity.of(clientEmailService.saveClientEmailFromDTO(emailDTO));
    }

    @PutMapping("/email/update")
    public ResponseEntity<?> updateClientEmail(@RequestBody ClientEmailDTO emailDTO) {
        Objects.requireNonNull(emailDTO, "Body must not be null");

        final String clientId = nzTrim(emailDTO.getClientId());
        final String email    = nzTrim(emailDTO.getEmailAddressTx());

        log.info("Update email requested for clientId={}", clientId);

        validateClientIdOrThrow(clientId);
        ensureClientExistsOrThrow(clientId);
        validateEmailOrThrow(email);

        boolean existsUnderClient = clientEmailService.clientEmailExists(clientId, email);
        if (!existsUnderClient) {
            throw new ClientEmailNotFoundException("Email not found for clientId " + clientId, email);
        }

        return ResponseEntity.of(clientEmailService.saveClientEmailFromDTO(emailDTO));
    }

    @DeleteMapping("/email/delete")
    public ResponseEntity<String> deleteEmail(
            @RequestParam String clientId,
            @RequestParam String emailAddress
    ) {
        final String cid   = nzTrim(clientId);
        final String email = nzTrim(emailAddress);

        log.info("Delete email requested for clientId={}, email={}", cid, email);

        validateClientIdOrThrow(cid);
        ensureClientExistsOrThrow(cid);
        validateEmailOrThrow(email);

        boolean existsUnderClient = clientEmailService.clientEmailExists(cid, email);
        if (!existsUnderClient) {
            throw new ClientEmailNotFoundException("Email not found for clientId " + cid, email);
        }

        clientEmailService.deleteClientEmailById(cid, email);
        return ResponseEntity.ok("Email deleted successfully.");
    }

    // (Optional) A lightweight HEAD-style existence check if your UI ever needs it
    @GetMapping("/email/exists")
    public ResponseEntity<Void> emailExists(
            @RequestParam String clientId,
            @RequestParam String emailAddress
    ) {
        final String cid   = nzTrim(clientId);
        final String email = nzTrim(emailAddress);

        validateClientIdOrThrow(cid);
        ensureClientExistsOrThrow(cid);
        validateEmailOrThrow(email);

        return clientEmailService.clientEmailExists(cid, email)
                ? ResponseEntity.ok().build()
                : ResponseEntity.status(HttpStatus.NOT_FOUND).build();
    }
}
