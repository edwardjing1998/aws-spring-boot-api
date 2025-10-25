package rapid.repository.sysprin;

import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;
import rapid.model.sysprin.key.VendorSentToId;
import rapid.model.sysprin.vendor.VendorReceivedFrom;

@Repository
public interface VendorReceivedFromRepository extends JpaRepository<VendorReceivedFrom, VendorSentToId> {
}




package rapid.model.sysprin.key;

import jakarta.persistence.Column;
import jakarta.persistence.Embeddable;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

import java.io.Serializable;

@Embeddable
@Data
@NoArgsConstructor
@AllArgsConstructor
public class VendorSentToId implements Serializable {

    @Column(name = "vend_id", length = 3, nullable = false)
    private String vendorId;

    @Column(name = "sys_prin", length = 12, nullable = false)
    private String sysPrin;


}



package rapid.model.sysprin.vendor;

import jakarta.persistence.*;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import rapid.model.sysprin.base.BaseVendorSentTo;
import rapid.model.sysprin.key.VendorSentToId;

@Entity
@Table(name = "vendor_sent_to")
@NoArgsConstructor
@Data
public class VendorReceivedFrom extends BaseVendorSentTo {
    public VendorReceivedFrom(VendorSentToId id, Boolean queForMail) {
        super(id, queForMail);
    }

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "VEND_ID", referencedColumnName = "VEND_ID", insertable = false, updatable = false)
    private Vendor vendor;
}





