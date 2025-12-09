public Page<ClientReportOptionDTO> getAllWithDetails(int page, int size) {
    // 1. Create Pageable object
    Pageable pageable = PageRequest.of(page, size);

    // 2. Fetch ONLY the slice of data needed from the DB
    // This returns a Page<ClientReportOption> with exactly 'size' (e.g. 20) items.
    Page<ClientReportOption> optionPage = clientReportOptionRepository.findAll(pageable);

    // 3. Optimization: Collect only the Report IDs relevant to this specific page
    // We do this to batch-fetch the details (AdminQueryList, C3) efficiently.
    List<Integer> relevantReportIds = optionPage.stream()
            .map(ClientReportOption::getReportId) // Assuming getReportId() exists
            .distinct()
            .collect(Collectors.toList());

    // 4. Fetch the bundle ONLY for the reports on this page (Efficient!)
    ClientReportOptionDataBundle bundle = dataFetcher.fetchForReports(relevantReportIds);

    // 5. Map the entities to DTOs using the bundle
    return optionPage.map(option -> {
        Integer reportId = option.getReportId();
        
        AdminQueryList admin = bundle.adminQueryList().get(reportId);
        C3FileTransfer c3 = bundle.c3FileTransfer().get(reportId);

        // Safety check if admin/c3 are missing
        if (admin != null) {
            option.setReportDetails(admin);
            if (c3 != null) {
                admin.setC3FileTransfer(c3);
            }
        }
        
        return optionMapper.toDto(option);
    });
}
