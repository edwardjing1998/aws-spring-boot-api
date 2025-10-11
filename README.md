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
