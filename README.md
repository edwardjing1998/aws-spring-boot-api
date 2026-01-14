package trace.sysPrin.mapper;

import org.mapstruct.InheritInverseConfiguration;
import org.mapstruct.Mapper;
import org.mapstruct.Mapping;
import org.mapstruct.Mappings;
import trace.dto.sysPrin.VendorReceivedFromDTO;
import trace.model.sysprin.vendor.VendorReceivedFrom;
import java.util.List;

@Mapper(componentModel = "spring", uses = VendorMapper.class)
public interface VendorReceivedFromMapper {

    /* ─────────────────────  entity ➜ DTO  ───────────────────── */
    @Mappings({
            @Mapping(target = "sysPrin",   source = "id.sysPrin"),
            @Mapping(target = "vendorId",  source = "id.vendorId"),
            @Mapping(target = "queForMail",source = "queForMail"),
            @Mapping(target = "vendor", source = "vendor") // uses VendorMapper
    })
    VendorReceivedFromDTO toDto(VendorReceivedFrom entity);

    List<VendorReceivedFromDTO> toDto(List<VendorReceivedFrom> entities);

    /* ─────────────────────  DTO ➜ entity  ───────────────────── */
    @InheritInverseConfiguration(name = "toDto")
    @Mappings({
            // build the composite id on-the-fly
            @Mapping(
                    target = "id",
                    expression = "java(new VendorSentToId(dto.getVendorId(), dto.getSysPrin()))"
            )
    })
    VendorReceivedFrom toEntity(VendorReceivedFromDTO dto);

    List<VendorReceivedFrom> toEntity(List<VendorReceivedFromDTO> dtos);
}
