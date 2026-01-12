select * from vendor v where v.Vend_id not in (select vend_id from Vendor_Sent_to vst where sys_prin = '57491000') and v.Vend_file_IO = 'O';


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

    @Query("""
           select v.vendId           as vendId,
                  v.vendName          as vendName,
                  v.vendReceiverCode      as vendReceiverCode,
                  v.fileServerName as fileServerName,
                  v.fileServerIp  as fileServerIp
             from Vendor v
            where v.active = true
              and v.fileIo = :io
           """)
    List<VendorTransferInformation> findActiveByFileIo(@Param("io") String fileIo);

    @Query("""
           select v.vendId           as vendId,
                  v.vendName         as vendName,
                  v.vendReceiverCode as vendReceiverCode,
                  v.fileServerName   as fileServerName,
                  v.fileServerIp     as fileServerIp
             from Vendor v
            where v.active = true
              and v.fileIo = :io
           """)
    Page<VendorTransferInformation> findActiveByFileIo(
            @Param("io") String fileIo,
            Pageable pageable
    );
}
