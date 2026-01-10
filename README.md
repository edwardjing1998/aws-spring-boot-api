package trace.repository.sysprin;

import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Modifying;
import org.springframework.data.jpa.repository.Query;
import org.springframework.data.repository.query.Param;
import org.springframework.stereotype.Repository;
import trace.model.sysprin.key.VendorSentToId;
import trace.model.sysprin.vendor.VendorReceivedFrom;

import java.util.Collection;
import java.util.List;

@Repository
public interface VendorReceivedFromRepository extends JpaRepository<VendorReceivedFrom, VendorSentToId> {

    @Modifying
    @Query("""
           delete from VendorReceivedFrom v
           where v.id.sysPrin = :sysPrin and v.id.vendorId = :vendorId
           """)
    int deleteOne(@Param("sysPrin") String sysPrin, @Param("vendorId") String vendorId);

    @Query("""
           select v from VendorReceivedFrom v
           where v.id.sysPrin = :sysPrin and v.id.vendorId = :vendorId
           """)
    List<VendorReceivedFrom> findBySysPrinVendorId(@Param("sysPrin") String sysPrin, @Param("vendorId") String vendorId);

    // (optional) delete all for a sysPrin
    @Modifying
    @Query("""
           delete from VendorReceivedFrom v
           where v.id.sysPrin = :sysPrin
           """)
    int deleteBySysPrin(@Param("sysPrin") String sysPrin);

    default VendorReceivedFrom addOne(String sysPrin, String vendorId, Boolean queForMail) {
        var id = new VendorSentToId(vendorId, sysPrin);
        var entity = new VendorReceivedFrom(id, queForMail);
        return save(entity);
    }

    List<VendorReceivedFrom> findByIdSysPrinIn(Collection<String> sysPrins);
}
