package your.package.service;

import your.package.domain.SysPrin;
import your.package.domain.InvalidDelivArea;
import your.package.domain.VendorSentTo;
import your.package.domain.VendorReceivedFrom;
import your.package.repository.InvalidDelivAreaRepository;
import your.package.repository.VendorSentToRepository;
import your.package.repository.VendorReceivedFromRepository;
import your.package.repository.VendorRepository;
import your.package.repository.SysPrinRepository;
import your.package.dto.VendorTransferInformation; // adjust import
import your.package.bundle.SysPrinDataBundle;       // your record/class

import java.util.*;
import java.util.function.Function;
import java.util.stream.Collectors;

import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Component;

@Component
@RequiredArgsConstructor
public class SysPrinDataFetcher {

    private final SysPrinRepository sysPrinRepository;
    private final InvalidDelivAreaRepository invalidDelivAreaRepository;
    private final VendorSentToRepository vendorSentToRepository;
    private final VendorReceivedFromRepository vendorReceivedFromRepository;
    private final VendorRepository vendorRepository;

    // helper to create key: "client|sys_prin"
    private String toKey(SysPrin sp) {
        return sp.getId().getClient() + "|" + sp.getId().getSysPrin();
    }

    // if duplicates, pick which sysPrin to keep
    private SysPrin prefer(SysPrin a, SysPrin b) {
        // simplest: keep the first one
        return a;
    }

    /**
     * NEW: Build SysPrinDataBundle from a given list of SysPrins (e.g. page content).
     */
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

    /**
     * OLD behaviour: for entire client (no paging).
     */
    public SysPrinDataBundle fetchSysPrinDataByClient(String client) {
        List<SysPrin> sysPrins = sysPrinRepository.findByIdClient(client);
        return fetchSysPrinDataForSysPrins(sysPrins);
    }
}
