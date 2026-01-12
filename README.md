package trace.repository.sysprin;

import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Query;
import org.springframework.data.repository.query.Param;
import org.springframework.stereotype.Repository;
import trace.model.sysprin.VendorTransferInformation;
import trace.model.sysprin.vendor.Vendor;

import java.util.List;

@Repository
public interface VendorRepository extends JpaRepository<Vendor, String> {

    /**
     * 你原先已有的：按 fileIo 找 active vendors（返回 projection）
     * 如果你原来方法名就是 findActiveByFileIo("O")，建议继续保留。
     *
     * ⚠️ 这里假设 Vendor 上有字段：
     *   - fileIo
     *   - active (或 vendActvCd)
     *   - vendId, vendName, vendReceiverCode, fileServerName, fileServerIp
     *
     * 如果字段名不同，我可以按你的 Vendor entity 实际字段名帮你改。
     */
    @Query("""
        select
          v.vendId           as vendId,
          v.vendName         as vendName,
          v.vendReceiverCode as vendReceiverCode,
          v.fileServerName   as fileServerName,
          v.fileServerIp     as fileServerIp
        from Vendor v
        where lower(v.fileIo) = lower(:fileIo)
          and (v.active = true or v.active = 1 or v.active = '1')
        order by v.vendId
    """)
    List<VendorTransferInformation> findActiveByFileIo(@Param("fileIo") String fileIo);

    /**
     * ✅ 你要的新需求：
     * fileIo='O' 且 vend_id 不在 Vendor_Sent_to(=VendorSentTo) 里（按 sysPrin 过滤）
     * 返回 VendorTransferInformation projection（只拿 5 个字段）
     */
    @Query("""
        select
          v.vendId           as vendId,
          v.vendName         as vendName,
          v.vendReceiverCode as vendReceiverCode,
          v.fileServerName   as fileServerName,
          v.fileServerIp     as fileServerIp
        from Vendor v
        where lower(v.fileIo) = lower(:fileIo)
          and not exists (
              select 1
              from VendorSentTo vst
              where vst.id.sysPrin = :sysPrin
                and vst.id.vendorId = v.vendId
          )
        order by v.vendId
    """)
    List<VendorTransferInformation> findNotInVendorSentToBySysPrinAndFileIo(
            @Param("sysPrin") String sysPrin,
            @Param("fileIo") String fileIo
    );

    /**
     * ✅ 保留第二个方法：分页版本（0,10 就是 PageRequest.of(0,10)）
     */
    @Query("""
        select
          v.vendId           as vendId,
          v.vendName         as vendName,
          v.vendReceiverCode as vendReceiverCode,
          v.fileServerName   as fileServerName,
          v.fileServerIp     as fileServerIp
        from Vendor v
        where lower(v.fileIo) = lower(:fileIo)
          and not exists (
              select 1
              from VendorSentTo vst
              where vst.id.sysPrin = :sysPrin
                and vst.id.vendorId = v.vendId
          )
    """)
    Page<VendorTransferInformation> findNotInVendorSentToBySysPrinAndFileIoPaged(
            @Param("sysPrin") String sysPrin,
            @Param("fileIo") String fileIo,
            Pageable pageable
    );
}
