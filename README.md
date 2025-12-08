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


        package rapid.model.sysprin.base;

import jakarta.persistence.*;
import lombok.Data;
import rapid.model.sysprin.key.SysPrinId;

@MappedSuperclass
@Data
public class BaseSysPrin {

    @EmbeddedId
    private SysPrinId id;

    @Column(name = "CUST_TYPE") private String custType;
    @Column(name = "UNDELIVERABLE") private String undeliverable;
    @Column(name = "STAT_A") private String statA;
    @Column(name = "STAT_B") private String statB;
    @Column(name = "STAT_C") private String statC;
    @Column(name = "STAT_D") private String statD;
    @Column(name = "STAT_E") private String statE;
    @Column(name = "STAT_F") private String statF;
    @Column(name = "STAT_I") private String statI;
    @Column(name = "STAT_L") private String statL;
    @Column(name = "STAT_O") private String statO;
    @Column(name = "STAT_U") private String statU;
    @Column(name = "STAT_X") private String statX;
    @Column(name = "STAT_Z") private String statZ;

    @Column(name = "PO_BOX") private String poBox;
    @Column(name = "ADDR_FLAG") private String addrFlag;
    @Column(name = "TEMP_AWAY") private Long tempAway;
    @Column(name = "RPS") private String rps;
    @Column(name = "SESSION") private String session;
    @Column(name = "BAD_STATE") private String badState;
    @Column(name = "A_STAT_RCH") private String astatRch;
    @Column(name = "NM_13") private String nm13;
    @Column(name = "TEMP_AWAY_ATTS") private Long tempAwayAtts;
    @Column(name = "REPORT_METHOD") private Double reportMethod;
    @Column(name = "ACTIVE") private String sysPrinActive;
    @Column(name = "NOTES") private String notes;
    @Column(name = "RET_STAT") private String returnStatus;
    @Column(name = "DES_STAT") private String destroyStatus;
    @Column(name = "NON_US") private String nonUS;
    @Column(name = "SPECIAL") private String special;
    @Column(name = "PIN") private String pinMailer;
    @Column(name = "FORWARDING_ADDR") private String forwardingAddress;
    @Column(name = "HOLD_DAYS") private Integer holdDays;
    @Column(name = "CONTACT") private String sysPrinContact;
    @Column(name = "PHONE") private String sysPrinPhone;
    @Column(name = "ENTITY_CD") private String entityCode;
}



package rapid.model.sysprin.key;

import jakarta.persistence.Column;
import jakarta.persistence.Embeddable;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

import java.io.Serializable;
import java.util.Objects;

@Embeddable
@Data
@NoArgsConstructor
@AllArgsConstructor
public class SysPrinId implements Serializable {

    @Column(name = "client")
    private String client;

    @Column(name = "sys_prin")
    private String sysPrin;

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof SysPrinId)) return false;
        SysPrinId that = (SysPrinId) o;
        return Objects.equals(client, that.client) &&
                Objects.equals(sysPrin, that.sysPrin);
    }

    @Override
    public int hashCode() {
        return Objects.hash(client, sysPrin);
    }
}
