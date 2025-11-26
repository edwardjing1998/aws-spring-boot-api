package rapid.sysPrin.mapper;

import org.mapstruct.InheritInverseConfiguration;
import org.mapstruct.Mapper;
import org.mapstruct.Mapping;
import org.mapstruct.Mappings;
import rapid.dto.sysPrin.VendorSentToDTO;
import rapid.model.sysprin.vendor.VendorSentTo;

import java.util.List;

@Mapper(componentModel = "spring", uses = VendorMapper.class)
public interface VendorSentToMapper {

    /* ─────────────────────  entity ➜ DTO  ───────────────────── */
    @Mappings({
            @Mapping(target = "sysPrin",   source = "id.sysPrin"),
            @Mapping(target = "vendorId",  source = "id.vendorId"),
            @Mapping(target = "queForMail",source = "queForMail"),
            @Mapping(target = "vendor", source = "vendor") // uses VendorMapper
    })
    VendorSentToDTO toDto(VendorSentTo entity);

    List<VendorSentToDTO> toDto(List<VendorSentTo> entities);

    /* ─────────────────────  DTO ➜ entity  ───────────────────── */
    @InheritInverseConfiguration(name = "toDto")
    @Mappings({
            // build the composite id on-the-fly
            @Mapping(
                    target = "id",
                    expression = "java(new VendorSentToId(dto.getVendorId(), dto.getSysPrin()))"
            )
    })
    VendorSentTo toEntity(VendorSentToDTO dto);

    List<VendorSentTo> toEntity(List<VendorSentToDTO> dtos);
}


[ERROR] Failed to execute goal org.apache.maven.plugins:maven-compiler-plugin:3.13.0:compile (default-compile) on project common-mapper: Compilation failure
[ERROR] /C:/Users/F2LIPBX/spring_boot/harish/trace-clients/common-mapper/src/main/java/rapid/sysPrin/mapper/VendorSentToMapper.java:[35,18] Can't map property "String sysPrin" to "SysPrin sysPrin". Consider to declare/implement a mapping method: "SysPrin map(String value)".  



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







