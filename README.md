package trace.model.sysprin.vendor;

import jakarta.persistence.*;
import lombok.Data;
import lombok.NoArgsConstructor;
import trace.model.sysprin.base.BaseVendorSentTo;
import trace.model.sysprin.key.VendorSentToId;

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


package trace.model.sysprin.base;

import jakarta.persistence.Column;
import jakarta.persistence.Id;
import jakarta.persistence.MappedSuperclass;
import lombok.Data;

@MappedSuperclass
@Data
public abstract class BaseVendor {

    @Id
    @Column(name = "vend_id", length = 3, nullable = false)
    private String vendId;

    @Column(name = "vend_nm", length = 50, nullable = false)
    private String vendName;

    @Column(name = "vend_actv_cd", nullable = false)
    private Boolean active;

    @Column(name = "vend_rcvr_cd", nullable = false)
    private Boolean vendReceiverCode;

    @Column(name = "vend_fsrv_nm", length = 40)
    private String fileServerName;

    @Column(name = "vend_fsrv_ip", length = 15)
    private String fileServerIp;

    @Column(name = "fsrvr_user_id", length = 10)
    private String fileServerUserId;

    @Column(name = "fsrvr_usr_pwd_tx", length = 50)
    private String fileServerPassword;

    @Column(name = "fsrvr_file_name_tx", length = 50)
    private String fileName;

    @Column(name = "fsrvr_unc_share_tx", length = 255)
    private String uncShare;

    @Column(name = "fsrvr_path_tx", length = 50)
    private String path;

    @Column(name = "fsrvr_file_archive_path_tx", length = 50)
    private String archivePath;

    @Column(name = "vendor_type_cd", length = 1)
    private String vendorTypeCode;

    @Column(name = "vend_file_io", length = 1)
    private String fileIo;

    @Column(name = "vend_client_specific")
    private Boolean clientSpecific;

    @Column(name = "vend_schedule", length = 8)
    private String schedule;

    @Column(name = "vend_date_last_worked", length = 10)
    private String dateLastWorked;

    @Column(name = "vend_filesize")
    private Integer fileSize;

    @Column(name = "vend_filetrans_specs", length = 50)
    private String fileTransferSpecs;

    @Column(name = "vend_file_type")
    private Integer fileType;

    @Column(name = "ftp_passive", length = 5)
    private String ftpPassive;

    @Column(name = "ftp_filetype", length = 1)
    private String ftpFileType;
}
