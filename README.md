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
            //         @Mapping(target = "invalidDelivAreas", source = "invalidDelivAreas"),
            //         @Mapping(target = "vendorSentTo", source = "vendorSentTo"),
            //         @Mapping(target = "vendorReceivedFrom", source = "vendorReceivedFrom")
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




        @Mapping(target = "invalidDelivAreas", source = "invalidDelivAreas"),
        @Mapping(target = "vendorSentTo", source = "vendorSentTo"),
        @Mapping(target = "vendorReceivedFrom", source = "vendorReceivedFrom")







