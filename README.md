package common.service.sysprin.fetcher;

import lombok.Data;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;
import trace.model.sysprin.InvalidDelivArea;
import trace.model.sysprin.SysPrin;
import trace.model.sysprin.VendorTransferInformation;
import trace.model.sysprin.vendor.VendorReceivedFrom;
import trace.model.sysprin.vendor.VendorSentTo;
import trace.sysPrin.mapper.fetch.SysPrinDataBundle;

import java.util.*;
import java.util.function.Function;
import java.util.stream.Collectors;
import trace.repository.sysprin.InvalidDelivAreaRepository;
import trace.repository.sysprin.SysPrinRepository;
import trace.repository.sysprin.VendorReceivedFromRepository;
import trace.repository.sysprin.VendorRepository;
import trace.repository.sysprin.VendorSentToRepository;

@Service
@RequiredArgsConstructor
@Data
public class SysPrinDataFetcher {

    private final SysPrinRepository sysPrinRepository;
    private final InvalidDelivAreaRepository invalidDelivAreaRepository;
    private final VendorSentToRepository vendorSentToRepository;
    private final VendorReceivedFromRepository vendorReceivedFromRepository;
    private final VendorRepository vendorRepository;

    public SysPrinDataBundle fetchSysPrinData() {
        // Step 1: Load all main data
        List<SysPrin> sysPrins = sysPrinRepository.findAll();
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


    public SysPrinDataBundle fetchSysPrinDataForSysPrins(List<SysPrin> sysPrins) {

        if (sysPrins == null || sysPrins.isEmpty()) {
            return new SysPrinDataBundle(
                    Collections.emptyMap(),
                    Collections.emptyMap(),
                    Collections.emptyMap(),
                    Collections.emptyMap(),
                    Collections.emptyMap(),
                    Collections.emptyMap()
            );
        }

        // all sysPrin keys in this page
        Set<String> sysPrinKeys = sysPrins.stream()
                .map(sp -> sp.getId().getSysPrin())
                .collect(Collectors.toCollection(LinkedHashSet::new));

        // areas for only these sysPrins
        List<InvalidDelivArea> areas =
                invalidDelivAreaRepository.findByIdSysPrinIn(sysPrinKeys);

        // sentTo for only these sysPrins (but we will still filter vendor.fileIo later)
        List<VendorSentTo> sentToRaw =
                vendorSentToRepository.findByIdSysPrinIn(sysPrinKeys);

        // receivedFrom for only these sysPrins
        List<VendorReceivedFrom> receivedFromRaw =
                vendorReceivedFromRepository.findByIdSysPrinIn(sysPrinKeys);

        // Filter sentTo: only include fileIo = 'I'
        List<VendorSentTo> sentTo = sentToRaw.stream()
                .filter(v -> v.getVendor() != null && "I".equalsIgnoreCase(v.getVendor().getFileIo()))
                .toList();

        // Filter receivedFrom: only include fileIo = 'O'
        List<VendorReceivedFrom> receivedFrom = receivedFromRaw.stream()
                .filter(v -> v.getVendor() != null && "O".equalsIgnoreCase(v.getVendor().getFileIo()))
                .toList();

        // vendor name maps (same as before)
        Map<String, String> vendorSentToIdToName = vendorRepository.findActiveByFileIo("I").stream()
                .collect(Collectors.toMap(
                        VendorTransferInformation::getVendId,
                        VendorTransferInformation::getVendName,
                        (a, b) -> a,
                        LinkedHashMap::new
                ));

        Map<String, String> vendorReceivedFromIdToName = vendorRepository.findActiveByFileIo("O").stream()
                .collect(Collectors.toMap(
                        VendorTransferInformation::getVendId,
                        VendorTransferInformation::getVendName,
                        (a, b) -> a,
                        LinkedHashMap::new
                ));

        // collapse duplicates for SysPrin
        Map<String, SysPrin> sysPrinMap = sysPrins.stream()
                .collect(Collectors.toMap(
                        this::toKey,
                        Function.identity(),
                        this::prefer,
                        LinkedHashMap::new
                ));

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


    private String toKey(SysPrin s) {
        return s.getId().getClient() + "|" + s.getId().getSysPrin();
    }

    /**
     * Merge rule when duplicate (client|sys_prin) rows exist:
     * 1) Prefer the one with active == "1".
     * 2) Otherwise, keep the first encountered.
     */
    private SysPrin prefer(SysPrin a, SysPrin b) {
        // UPDATED: Check for String "1" instead of Boolean object
        boolean aActive = "1".equals(a.getSysPrinActive());
        boolean bActive = "1".equals(b.getSysPrinActive());
        if (aActive && !bActive) return a;
        if (!aActive && bActive) return b;

        // tie-breaker: keep the first (i.e., 'a')
        return a;
    }
}
