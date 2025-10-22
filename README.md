// imports you’ll likely need:
//
// import org.springframework.http.ResponseEntity;
// import org.springframework.validation.annotation.Validated;
// import org.springframework.web.bind.annotation.*;
// import jakarta.validation.constraints.NotBlank;
// import jakarta.validation.constraints.Size;
// import java.net.URI;

@PutMapping("/{client}/{sysPrin}")
public ResponseEntity<BulkUpdateResponse> updateSysPrin(
    @PathVariable
    @NotBlank @Size(max = 4,  message = "client must be 1–4 characters")
    String client,

    @PathVariable
    @NotBlank @Size(max = 12, message = "sysPrin must be 1–12 characters")
    String sysPrin,

    @Valid @RequestBody SysPrinCreateRequest req
) {
    // Enforce the path variables as source of truth
    req.setClient(client);
    req.setSysPrin(sysPrin);

    // 1) Validate client exists (404 if not)
    if (clientService.clientIsExist(client).isEmpty()) {
        throw new ResponseStatusException(NOT_FOUND, "Client not found: " + client);
    }

    // 2) Validate at least one sys_prins row exists for this business key (404)
    if (sysPrinService.findByClientSysPrin(client, sysPrin).isEmpty()) {
        throw new ResponseStatusException(
            NOT_FOUND,
            "SysPrin does not exist for client=" + client + ", sysPrin=" + sysPrin
        );
    }

    // 3) Bulk update all duplicates with same (client, sysPrin)
    int rowsUpdated = sysPrinService.updateAllByClientSysPrin(req);

    // 4) Return 200 OK with a small summary payload
    BulkUpdateResponse resp = new BulkUpdateResponse(client, sysPrin, rowsUpdated);
    return ResponseEntity.ok(resp);
}

/** Tiny response DTO so callers know how many rows were touched. */
public record BulkUpdateResponse(String client, String sysPrin, int rowsUpdated) {}
