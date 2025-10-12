@PostMapping("email/add")
public ResponseEntity<?> addClientEmail(@RequestBody ClientEmailDTO emailDTO) {

    final String clientId = emailDTO.getClientId() == null ? "" : emailDTO.getClientId().trim();
    final String email    = emailDTO.getEmailAddressTx() == null ? "" : emailDTO.getEmailAddressTx().trim();

    log.info("the client id {} requested for add email", clientId);

    // 1) clientId format
    if (!CLINT_ID_PATTERN.matcher(clientId).matches()) {
        log.info("the client id added {} is invalid", clientId);
        throw new ClientBadRequestException(
            "Invalid 'clientNumber': must be exactly 4 digits and can not contains alphabetic characters"
        );
    }

    // 2) existence check
    if (!clientRepository.existsById(clientId)) {
        // no throw at the end, but here we still want a clean 404 for non-existing client
        return ResponseEntity.status(HttpStatus.NOT_FOUND)
                .body("Client not found: " + clientId);
    }

    // 3) email format
    if (!EMAIL_PATTERN.matcher(email).matches()) {
        throw new ClientBadRequestException("Invalid email address to add: " + email);
    }

    // 4) save — no orElseThrow; 200 if present, 404 if empty
    return ResponseEntity.of(clientEmailService.saveClientEmailFromDTO(emailDTO));
}
