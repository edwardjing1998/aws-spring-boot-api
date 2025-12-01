package your.package.service;

import your.package.dto.SysPrinDTO;
import your.package.dto.PageResponse;
import your.package.domain.SysPrin;
import your.package.bundle.SysPrinDataBundle;
import your.package.service.SysPrinDataFetcher;
import your.package.repository.SysPrinRepository;
import your.package.domain.InvalidDelivArea;
import your.package.domain.VendorSentTo;
import your.package.domain.VendorReceivedFrom;
import your.package.dto.VendorSentToDTO;
import your.package.dto.VendorReceivedFromDTO;
import your.package.mapper.VendorSentToMapper;
import your.package.mapper.VendorReceivedFromMapper;
import your.package.service.DtoEnricher;

import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;

import org.springframework.data.domain.Page;
import org.springframework.data.domain.PageRequest;
import org.springframework.data.domain.Pageable;
import org.springframework.data.domain.Sort;
import org.springframework.stereotype.Service;
import org.springframework.web.server.ResponseStatusException;

import static org.springframework.http.HttpStatus.NOT_FOUND;

import java.util.List;

@Service
@RequiredArgsConstructor
@Slf4j
public class SysPrinService {

    private final SysPrinRepository sysPrinRepository;
    private final SysPrinDataFetcher sysPrindataFetcher;
    private final VendorSentToMapper vendorSentToMapper;
    private final VendorReceivedFromMapper vendorReceivedFromMapper;
    private final DtoEnricher dtoEnricher; // your existing service

    // existing getByClient(...) left untouched...

    /**
     * NEW: DB-level paginated retrieval of SysPrins for a client.
     */
    public PageResponse<SysPrinDTO> getByClientPaged(String client, int page, int size) {
        if (size <= 0) size = 20;
        if (page < 0) page = 0;

        Pageable pageable = PageRequest.of(page, size, Sort.by("id.client").ascending()
                                                                .and(Sort.by("id.sysPrin").ascending()));

        Page<SysPrin> sysPrinPage = sysPrinRepository.findByIdClient(client, pageable);

        if (sysPrinPage.isEmpty()) {
            throw new ResponseStatusException(NOT_FOUND, "No SysPrins found for client: " + client);
        }

        // Build bundle only for this page's SysPrins
        List<SysPrin> pageContent = sysPrinPage.getContent();
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

        return new PageResponse<>(dtoList, page, size, total);
    }
}
