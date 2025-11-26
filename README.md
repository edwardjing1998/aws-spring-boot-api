package rapid.model.sysprin;

import jakarta.persistence.*;
import lombok.Data;
import rapid.model.sysprin.base.BaseSysPrin;

@Entity
@Table(name = "SYS_PRINS")
public class SysPrin  extends BaseSysPrin {
}


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






package rapid.model.sysprin.base;

import jakarta.persistence.Column;
import jakarta.persistence.EmbeddedId;
import jakarta.persistence.MappedSuperclass;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import rapid.model.sysprin.key.VendorSentToId;

@MappedSuperclass
@Data
@AllArgsConstructor
@NoArgsConstructor
public abstract class BaseVendorSentTo {

    @EmbeddedId
    private VendorSentToId id;

    @Column(name = "queformail_cd", nullable = false)
    private Boolean queForMail;
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






