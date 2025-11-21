    @GetMapping("/report-option")
    public ResponseEntity<List<ClientReportOptionDTO>> getAllReports() {
        List<ClientReportOptionDTO> dtos = service.getAllWithDetails();
        return ResponseEntity.ok(dtos);
    }





    public List<ClientReportOptionDTO> getAllWithDetails() {

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



    package common.service.client.fetcher;

import lombok.Getter;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Component;
import trace.client.mapper.fetch.ClientReportOptionDataBundle;
import trace.model.client.AdminQueryList;
import trace.model.client.C3FileTransfer;
import trace.model.client.ClientReportOption;
import trace.repository.client.AdminQueryListRepository;
import trace.repository.client.C3FileTransferRepository;
import trace.repository.client.ClientReportOptionRepository;

import java.util.List;
import java.util.Map;
import java.util.function.Function;
import java.util.stream.Collectors;

/**
 * Bulk-loads {@link AdminQueryList}, related {@link C3FileTransfer}, and {@link ClientReportOption}
 * rows for the supplied <b>reportIds</b> in one shot and returns them
 * grouped inside a {@link ClientReportOptionDataBundle}.
 */
@Component
@RequiredArgsConstructor
@Getter
public class ClientReportOptionDataFetcher {

    private final AdminQueryListRepository adminRepo;
    private final C3FileTransferRepository c3Repo;
    private final ClientReportOptionRepository optionRepo;

    public ClientReportOptionDataBundle fetchForReports(List<Integer> reportIds) {

        // ─── Load BaseAdminQueryList (main entities) ─────────────────────────────
        Map<Integer, AdminQueryList> adminMap = adminRepo.findAllById(reportIds).stream()
                .collect(Collectors.toMap(AdminQueryList::getReportId, Function.identity()));

        // ─── Load C3FileTransfer (1-to-1 by fileTransId = reportId) ──────────
        Map<Integer, C3FileTransfer> c3Map = c3Repo.findAllById(reportIds).stream()
                .collect(Collectors.toMap(C3FileTransfer::getFileTransId, Function.identity()));

        // ─── Load ClientReportOption (1-to-many by reportId) ─────────────────
        Map<Integer, List<ClientReportOption>> optionMap = optionRepo.findByIdReportIdIn(reportIds).stream()
                .collect(Collectors.groupingBy(o -> o.getId().getReportId()));

        return new ClientReportOptionDataBundle(adminMap, c3Map, optionMap);
    }

}
    
