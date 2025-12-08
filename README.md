    public SysPrinPaginationResponse<SysPrinDTO> getByClientPaged(String client, int page, int size) {
        if (size <= 0) size = 20;
        if (page < 0) page = 0;

        Pageable pageable = PageRequest.of(page, size, Sort.by("id.client").ascending()
                .and(Sort.by("id.sysPrin").ascending()));

        Page<SysPrin> sysPrinPage = sysPrinRepository.findByIdClient(client, pageable);

        if (sysPrinPage.isEmpty()) {
            throw new ResponseStatusException(NOT_FOUND, "No SysPrins found for client: " + client);
        }

        // --- 1) raw page content from DB (may contain duplicates) ---
        List<SysPrin> rawPageContent = sysPrinPage.getContent();

        // --- 2) collapse duplicates within this page, keyed by (client|sysPrin) ---
        Map<String, SysPrin> uniqueMap = rawPageContent.stream()
                .collect(Collectors.toMap(
                        this::toKey,          // key: "client|sysPrin"
                        Function.identity(),  // value: SysPrin
                        this::prefer,         // merge duplicates
                        LinkedHashMap::new    // keep original encounter order
                ));

        List<SysPrin> pageContent = new ArrayList<>(uniqueMap.values());

        SysPrinDataBundle bundle = sysPrindataFetcher.fetchSysPrinDataForSysPrins(pageContent);

        List<SysPrinDTO> dtoList = pageContent.stream()
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

        long total = sysPrinPage.getTotalElements();

        return new SysPrinPaginationResponse<>(dtoList, page, size, total);
    }



        Page<SysPrin> findByIdClient(String client, Pageable pageable);
