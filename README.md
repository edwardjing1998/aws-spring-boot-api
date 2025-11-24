@GetMapping("/report-option/pagination")
    public ResponseEntity<List<ClientReportOptionDTO>> getAllReports(
            @RequestParam(defaultValue = "0") int page,
            @RequestParam(defaultValue = "10") int size) {

        Pageable pageable = PageRequest.of(page, size);
        Page<ClientReportOptionDTO> dtoPage = service.getAllWithDetails(pageable);

        return ResponseEntity.ok(dtoPage.getContent());
    }
