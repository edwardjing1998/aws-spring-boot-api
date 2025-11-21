import org.springframework.data.domain.Page;
import org.springframework.data.domain.PageImpl;
import org.springframework.data.domain.Pageable;

// ...

public Page<ClientReportOptionDTO> getAllWithDetails(Pageable pageable) {

    // 1) Page on AdminQueryList (the "report" entities)
    Page<AdminQueryList> adminPage = dataFetcher.getAdminRepo().findAll(pageable);

    List<Integer> reportIds = adminPage.getContent().stream()
            .map(AdminQueryList::getReportId)
            .toList();

    if (reportIds.isEmpty()) {
        return Page.empty(pageable);
    }

    // 2) Bulk fetch all related data for these reportIds
    ClientReportOptionDataBundle bundle = dataFetcher.fetchForReports(reportIds);

    // 3) Same logic you already have, but limited to this page’s reportIds
    List<ClientReportOptionDTO> dtos = reportIds.stream()
            .flatMap(id -> {
                AdminQueryList admin = bundle.adminQueryList().get(id);
                C3FileTransfer c3 = bundle.c3FileTransfer().get(id);
                List<ClientReportOption> options = bundle.clientReportOptions().get(id);

                if (options == null || options.isEmpty()) return Stream.empty();

                return options.stream().map(option -> {
                    option.setReportDetails(admin);
                    if (admin != null) {
                        admin.setC3FileTransfer(c3);
                    }
                    return optionMapper.toDto(option);
                });
            })
            .toList();

    // 4) Wrap in a Page. For total count, we use adminPage’s total elements
    return new PageImpl<>(dtos, pageable, adminPage.getTotalElements());
}
