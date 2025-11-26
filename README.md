package rapid.model.sysprin;

import jakarta.persistence.*;
import lombok.Data;
import rapid.model.sysprin.base.BaseSysPrin;
import rapid.model.sysprin.vendor.VendorReceivedFrom;
import rapid.model.sysprin.vendor.VendorSentTo;

import java.util.ArrayList;
import java.util.List;

@Entity
@Table(name = "SYS_PRINS")
@Data
public class SysPrin  extends BaseSysPrin {
    @OneToMany(mappedBy = "sysPrin", cascade = CascadeType.ALL, orphanRemoval = false)
    private List<VendorSentTo> vendorSentTo = new ArrayList<>();
}


package rapid.model.sysprin.vendor;

import jakarta.persistence.*;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import rapid.model.sysprin.SysPrin;
import rapid.model.sysprin.base.BaseVendorSentTo;
import rapid.model.sysprin.key.VendorSentToId;

@Entity
@Table(name = "vendor_sent_to")
@NoArgsConstructor
@Data
public class VendorSentTo extends BaseVendorSentTo {
    public VendorSentTo(VendorSentToId id, Boolean queForMail) {
        super(id, queForMail);
    }

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "VEND_ID", referencedColumnName = "VEND_ID", insertable = false, updatable = false)
    private Vendor vendor;

    // JOIN to SYS_PRINS on SYS_PRIN column (part of SysPrin PK)
    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(
            name = "SYS_PRIN",              // column in VENDOR_SENT_TO table
            referencedColumnName = "SYS_PRIN",
            insertable = false,
            updatable = false
    )
    private SysPrin sysPrin;
}




package rapid.sysPrin.mapper;

import org.mapstruct.InheritInverseConfiguration;
import org.mapstruct.Mapper;
import org.mapstruct.Mapping;
import org.mapstruct.Mappings;
import org.mapstruct.ReportingPolicy;
import rapid.dto.sysPrin.SysPrinCreateRequest;
import rapid.dto.sysPrin.SysPrinDTO;
import rapid.dto.sysPrin.SysPrinWithInvalidDelivAreaResponseDTO;
import rapid.model.sysprin.SysPrin;

@Mapper(
        componentModel = "spring",
        uses = {
                InvalidDelivAreaMapper.class,
                VendorSentToMapper.class,
                VendorReceivedFromMapper.class
        },
        unmappedTargetPolicy = ReportingPolicy.IGNORE
)
public interface SysPrinMapper {

    /* ──────────────── DTO ➜ ENTITY ──────────────── */
    @Mappings({
            @Mapping(target = "id.client", source = "client"),
            @Mapping(target = "id.sysPrin", source = "sysPrin"),

            @Mapping(target = "custType", source = "custType"),
            @Mapping(target = "undeliverable", source = "undeliverable"),
            @Mapping(target = "statA", source = "statA"),
            @Mapping(target = "statB", source = "statB"),
            @Mapping(target = "statC", source = "statC"),
            @Mapping(target = "statD", source = "statD"),
            @Mapping(target = "statE", source = "statE"),
            @Mapping(target = "statF", source = "statF"),
            @Mapping(target = "statI", source = "statI"),
            @Mapping(target = "statL", source = "statL"),
            @Mapping(target = "statO", source = "statO"),
            @Mapping(target = "statU", source = "statU"),
            @Mapping(target = "statX", source = "statX"),
            @Mapping(target = "statZ", source = "statZ"),

            @Mapping(target = "poBox", source = "poBox"),
            @Mapping(target = "addrFlag", source = "addrFlag"),
            @Mapping(target = "tempAway", source = "tempAway"),
            @Mapping(target = "rps", source = "rps"),
            @Mapping(target = "session", source = "session"),
            @Mapping(target = "badState", source = "badState"),
            @Mapping(target = "astatRch", source = "astatRch"),
            @Mapping(target = "nm13", source = "nm13"),
            @Mapping(target = "tempAwayAtts", source = "tempAwayAtts"),
            @Mapping(target = "reportMethod", source = "reportMethod"),
            @Mapping(target = "sysPrinActive", source = "sysPrinActive"),
            @Mapping(target = "notes", source = "notes"),
            @Mapping(target = "returnStatus", source = "returnStatus"),
            @Mapping(target = "destroyStatus", source = "destroyStatus"),
            @Mapping(target = "nonUS", source = "nonUS"),
            @Mapping(target = "special", source = "special"),
            @Mapping(target = "pinMailer", source = "pinMailer"),
            @Mapping(target = "holdDays", source = "holdDays"),
            @Mapping(target = "forwardingAddress", source = "forwardingAddress"),
            @Mapping(target = "sysPrinContact", source = "sysPrinContact"),
            @Mapping(target = "sysPrinPhone", source = "sysPrinPhone"),
            @Mapping(target = "entityCode", source = "entityCode"),

            // Related lists handled by their own mappers
//            @Mapping(target = "invalidDelivAreas", source = "invalidDelivAreas"),
            @Mapping(target = "vendorSentTo", source = "vendorSentTo"),
//            @Mapping(target = "vendorReceivedFrom", source = "vendorReceivedFrom")
    })
    SysPrin toEntity(SysPrinDTO dto);

    /* ──────────────── ENTITY ➜ DTO ──────────────── */
    @InheritInverseConfiguration
    SysPrinDTO toDto(SysPrin entity);

    @Mappings({
            @Mapping(target = "id.client", source = "client"),
            @Mapping(target = "id.sysPrin", source = "sysPrin"),

            @Mapping(target = "custType", source = "custType"),
            @Mapping(target = "undeliverable", source = "undeliverable"),

            @Mapping(target = "statA", source = "statA"),
            @Mapping(target = "statB", source = "statB"),
            @Mapping(target = "statC", source = "statC"),
            @Mapping(target = "statD", source = "statD"),
            @Mapping(target = "statE", source = "statE"),
            @Mapping(target = "statF", source = "statF"),
            @Mapping(target = "statI", source = "statI"),
            @Mapping(target = "statL", source = "statL"),
            @Mapping(target = "statO", source = "statO"),
            @Mapping(target = "statU", source = "statU"),
            @Mapping(target = "statX", source = "statX"),
            @Mapping(target = "statZ", source = "statZ"),

            @Mapping(target = "poBox", source = "poBox"),
            @Mapping(target = "addrFlag", source = "addrFlag"),
            @Mapping(target = "tempAway", source = "tempAway"),
            @Mapping(target = "rps", source = "rps"),
            @Mapping(target = "session", source = "session"),
            @Mapping(target = "badState", source = "badState"),
            @Mapping(target = "astatRch", source = "astatRch"),
            @Mapping(target = "nm13", source = "nm13"),
            @Mapping(target = "tempAwayAtts", source = "tempAwayAtts"),
            @Mapping(target = "reportMethod", source = "reportMethod"),
            @Mapping(target = "sysPrinActive", source = "sysPrinActive"),
            @Mapping(target = "notes", source = "notes"),
            @Mapping(target = "returnStatus", source = "returnStatus"),
            @Mapping(target = "destroyStatus", source = "destroyStatus"),
            @Mapping(target = "nonUS", source = "nonUS"),
            @Mapping(target = "special", source = "special"),
            @Mapping(target = "pinMailer", source = "pinMailer"),
            @Mapping(target = "holdDays", source = "holdDays"),
            @Mapping(target = "forwardingAddress", source = "forwardingAddress"),
            @Mapping(target = "sysPrinContact", source = "sysPrinContact"),
            @Mapping(target = "sysPrinPhone", source = "sysPrinPhone"),
            @Mapping(target = "entityCode", source = "entityCode")

            // EXCLUDED: clientIds, invalidDelivAreas, vendorSentTo, vendorReceivedFrom
    })
    SysPrin fromCreateRequest(SysPrinCreateRequest dto);

    SysPrinWithInvalidDelivAreaResponseDTO toSysPrinWithInvalidDelivAreaResponseDTO(SysPrin sp);
}



package rapid.sysPrin.mapper;

import org.mapstruct.Mapper;
import org.mapstruct.Mapping;
import org.mapstruct.Mappings;
import rapid.dto.sysPrin.VendorSentToDTO;
import rapid.model.sysprin.vendor.VendorSentTo;
import rapid.model.sysprin.key.VendorSentToId;

import java.util.List;

@Mapper(componentModel = "spring", uses = VendorMapper.class)
public interface VendorSentToMapper {

    /* ─────────────────────  entity ➜ DTO  ───────────────────── */
    @Mappings({
            // DTO.sysPrin comes from composite key
            @Mapping(target = "sysPrin",    source = "id.sysPrin"),
            @Mapping(target = "vendorId",   source = "id.vendorId"),
            @Mapping(target = "queForMail", source = "queForMail"),
            @Mapping(target = "vendor",     source = "vendor")  // uses VendorMapper
    })
    VendorSentToDTO toDto(VendorSentTo entity);

    List<VendorSentToDTO> toDto(List<VendorSentTo> entities);

    /* ─────────────────────  DTO ➜ entity  ───────────────────── */
    @Mappings({
            // Build composite id from DTO fields
            @Mapping(
                    target = "id",
                    expression = "java(new VendorSentToId(dto.getVendorId(), dto.getSysPrin()))"
            ),
            // ❗ Do NOT try to map String -> SysPrin automatically
            @Mapping(target = "sysPrin", ignore = true),
            // Usually you also ignore vendor here and set it in service if needed
            @Mapping(target = "vendor", ignore = true)
    })
    VendorSentTo toEntity(VendorSentToDTO dto);

    List<VendorSentTo> toEntity(List<VendorSentToDTO> dtos);
}






@ManyToOne(fetch = FetchType.LAZY)
@JoinColumns({
    @JoinColumn(
        name = "CLIENT",
        referencedColumnName = "CLIENT",
        insertable = false,
        updatable = false
    ),
    @JoinColumn(
        name = "SYS_PRIN",
        referencedColumnName = "SYS_PRIN",
        insertable = false,
        updatable = false
    )
})
private SysPrin sysPrin;




