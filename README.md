    @GetMapping("client/report-option")
    public ResponseEntity<List<ClientReportOptionDTO>> getAllReports(
            @RequestParam(defaultValue = "0") @Min(0) int page,
            @RequestParam(defaultValue = "20") @Min(1) int size
    ) {
        List<ClientReportOptionDTO> dtos = service.getAllWithDetails(page, size);
        return ResponseEntity.ok(dtos);
    }




        public List<ClientReportOptionDTO> getAllWithDetails(int page, int size) {

        List<Integer> reportIds = new ArrayList<>(
                dataFetcher.getAdminRepo().findAllReportIds()
        );

        ClientReportOptionDataBundle bundle = dataFetcher.fetchForReports(reportIds);

        return reportIds.stream()
                .flatMap(id -> {
                    AdminQueryList admin = bundle.adminQueryList().get(id);
                    C3FileTransfer c3 = bundle.c3FileTransfer().get(id);
                    List<ClientReportOption> options = bundle.clientReportOptions().get(id);

                    if (options == null) return Stream.empty();

                    return options.stream().map(option -> {
                        option.setReportDetails(admin);
                        admin.setC3FileTransfer(c3);
                        return optionMapper.toDto(option);
                    });
                })
                .collect(Collectors.toList());
    }
