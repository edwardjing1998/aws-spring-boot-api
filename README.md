package rapid.service.client.fetcher;

import lombok.RequiredArgsConstructor;
import org.springframework.data.domain.PageRequest;
import org.springframework.data.domain.Pageable;
import org.springframework.stereotype.Component;
import rapid.client.mapper.fetch.SysPrinsPrefixDataBundle;
import rapid.model.client.SysPrinsPrefix;
import rapid.repository.sysprin.SysPrinsPrefixRepository;

import java.util.List;
import java.util.Map;
import java.util.stream.Collectors;

/**
 * Bulk-loads SysPrinsPrefix records and groups them by billingSp.
 */
@Component
@RequiredArgsConstructor
public class SysPrinsPrefixDataFetcher {

    private final SysPrinsPrefixRepository prefixRepo;

    public List<SysPrinsPrefix> fetchByBillingSpPagination(String billingSp, int page, int size) {

        Pageable pageable = PageRequest.of(page, size);

        return prefixRepo.findByIdBillingSp(billingSp, pageable);
    }
}
