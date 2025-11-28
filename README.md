    @GetMapping("/client/{client}")
    public ResponseEntity<List<SysPrinDTO>> getByClient(@PathVariable @Size(max = 4, message = "ClientId should not be more than 4 characters") String client) {
        log.info("the client id requested {}", client);
        List<SysPrinDTO> list = sysPrinService.getByClient(client);
        log.info("the records found {} for the client - {}", list.size(), client);

        if (list.isEmpty()) {
            throw new ResponseStatusException(NOT_FOUND, "No SysPrins found for client: " + client);
        }
        return ResponseEntity.ok(list);
    }


    public List<SysPrinDTO> getByClient(String client) {

        SysPrinDataBundle bundle = sysPrindataFetcher.fetchSysPrinDataByClient(client);

        return bundle.sysPrins().values().stream()
                .filter(sp -> sp.getId() != null && client.equals(sp.getId().getClient()))
                .map(sp -> {
                    String spKey = sp.getId().getSysPrin();

                    List<InvalidDelivArea> areas =
                            bundle.invalidDelivAreas().getOrDefault(spKey, List.of());

                    List<VendorSentToDTO> sentToDtos =
                            vendorSentToMapper.toDto(
                                    bundle.vendorSentTo().getOrDefault(spKey, List.of()));

                    List<VendorReceivedFromDTO> recvDtos =
                            vendorReceivedFromMapper.toDto(
                                    bundle.vendorReceiveFrom().getOrDefault(spKey, List.of()));

                    return dtoEnricher.enrich(sp, areas, sentToDtos, recvDtos);
                })
                .toList();
    }




    public SysPrinDataBundle fetchSysPrinDataByClient(String client) {
        // Step 1: Load all main data
        List<SysPrin> sysPrins = sysPrinRepository.findByIdClient(client);
        List<InvalidDelivArea> areas = invalidDelivAreaRepository.findAll();

        // Filter sentTo: only include fileIo = 'I'
        List<VendorSentTo> sentTo = vendorSentToRepository.findAll().stream()
                .filter(v -> v.getVendor() != null && "I".equalsIgnoreCase(v.getVendor().getFileIo()))
                .toList();

        // Filter receivedFrom: only include fileIo = 'O'
        List<VendorReceivedFrom> receivedFrom = vendorReceivedFromRepository.findAll().stream()
                .filter(v -> v.getVendor() != null && "O".equalsIgnoreCase(v.getVendor().getFileIo()))
                .toList();

        // Step 2: Build vendorId → vendorName maps (with merge to avoid duplicate vendId)
        Map<String, String> vendorSentToIdToName = vendorRepository.findActiveByFileIo("I").stream()
                .collect(Collectors.toMap(
                        VendorTransferInformation::getVendId,
                        VendorTransferInformation::getVendName,
                        (a, b) -> a, // keep first if duplicate vendId occurs
                        LinkedHashMap::new
                ));

        Map<String, String> vendorReceivedFromIdToName = vendorRepository.findActiveByFileIo("O").stream()
                .collect(Collectors.toMap(
                        VendorTransferInformation::getVendId,
                        VendorTransferInformation::getVendName,
                        (a, b) -> a, // keep first if duplicate vendId occurs
                        LinkedHashMap::new
                ));

        // Step 3: Collapse duplicates: keep ONE SysPrin per (client|sys_prin)
        Map<String, SysPrin> sysPrinMap = sysPrins.stream()
                .collect(Collectors.toMap(
                        this::toKey,                 // key: "client|sys_prin"
                        Function.identity(),         // value: SysPrin itself
                        this::prefer,               // merge function for duplicate keys
                        LinkedHashMap::new           // keep stable encounter order
                ));

        // Step 4: Assemble all into the bundle
        return new SysPrinDataBundle(
                sysPrinMap,
                areas.stream().collect(Collectors.groupingBy(
                        a -> a.getId().getSysPrin()
                )),
                sentTo.stream().collect(Collectors.groupingBy(
                        v -> v.getId().getSysPrin()
                )),
                receivedFrom.stream().collect(Collectors.groupingBy(
                        v -> v.getId().getSysPrin()
                )),
                vendorSentToIdToName,
                vendorReceivedFromIdToName
        );
    }


    
