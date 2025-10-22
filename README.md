   @PostMapping("/update/{client}/{sysPrin}")
    public ResponseEntity<SysPrinDTO> updateSysPrin(
            @PathVariable @Size(max = 4, message = "ClientId should not be more than 4 characters") String client,
            @PathVariable @Size(max = 12, message = "sysPrin should not be more than 12 characters") String sysPrin,
            @Validated @RequestBody SysPrinCreateRequest req
    ) {
        // Move values from path variables to the body DTO (your original behavior)
        req.setClient(client);
        req.setSysPrin(sysPrin);

        // ===== Moved validation logic from Service to Controller =====
        if (req.getClient() == null || req.getSysPrin() == null) {
            throw new ResponseStatusException(BAD_REQUEST, "client and sysPrin are required");
        }

        List<Client> clientExists = clientService.clientIsExist(req.getClient());
        if (clientExists.isEmpty()) {
            throw new ResponseStatusException(NOT_FOUND, "Client not found: " + req.getClient());
        }

       List<SysPrin> sysPrins = sysPrinService.findByClientSysPrin(client, sysPrin);

        if (sysPrins.isEmpty()) {
            throw new ResponseStatusException(NOT_FOUND, "SysPrin does not exists, for client: " + client + " and sysPrin: " + sysPrin);
        }

        int updatedRow = sysPrinService.updateAllByClientSysPrin(req);

        URI location = ServletUriComponentsBuilder.fromCurrentRequestUri().build().toUri();
        return ResponseEntity.created(location).body(saved);
    }
