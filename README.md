    @DeleteMapping("/Delete")
    public ResponseEntity<String> deleteClient(@PathVariable @Size(max = 4, message = "ClientId should not be more than 4 characters") String clientId) {
        if (clientId == null || clientId.isBlank()) {
            throw new ClientBadRequestException("clientId is missing");
        }

        log.info("Deleting client details with ID: {}", clientId);
        String response = clientService.deleteClientDeep(clientId);
        log.info("Client details deleted successfully for ID: {}", clientId);
        return ResponseEntity.ok(response);
    }
