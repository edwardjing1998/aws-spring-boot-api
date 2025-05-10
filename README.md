package admin.model;

import jakarta.persistence.*;

@Entity
@Table(name = "c3_transfer_parameters")
public class C3FileTransfer {

    @Id
    @Column(name = "file_trns_id" , nullable = false)
    private Integer fileTransId;

    @Column(name = "sequence_nr", nullable = false)
    private Integer sequenceNr;

    @Column(name = "transfer_cd", nullable = false)
    private Integer transferCd;

    @Column(name = "protocol_nm", nullable = false, length = 100)
    private String protocolNm;

    @Column(name = "trans_prg_nm", nullable = false, length = 100)
    private String transPrgNm;

    @Column(name = "mode_nm", nullable = false, length = 100)
    private String modeNm;

    @Column(name = "security_nm", nullable = false, length = 100)
    private String securityNm;

    @Column(name = "ip_port_cd", nullable = false , length = 100)
    private String ipPortCd;

    @Column(name = "listener_srv_nm", nullable = false, length = 100)
    private String listenerSrvNm;

    @Column(name = "dd_nm", nullable = false, length = 100)
    private String ddNm;

    @Column(name = "org_type_cd", nullable = false, length = 100)
    private Integer OrgTypeCd;

    @Column(name = "member_cd" , nullable = false )
    private String memberCd;

    @Column(name = "record_lgth_nr", nullable = false)
    private Integer recordLgthNr;

    @Column(name = "block_size_nr", nullable = false)
    private Integer blockSizeNr;

    @Column(name = "convert_file_cd", nullable = false)
    private Integer convertFileCd;

    @Column(name = "xfer_file_nm" , nullable = false , length = 100)
    private String xferFileNm;

    @Column(name = "job_nm" , nullable = false , length = 100)
    private String jobNm;

    @Column(name = "program_nm", nullable = false , length = 100)
    private String programNm;

    @Column(name = "remote_file_nm", nullable = false , length = 100)
    private String remoteFileNm;

    @Column(name = "local_file_nm", nullable = false, length = 100)
    private String localFileNm;

    @Column(name = "control_file_nm", nullable = false, length = 100)
    private String controlFileNm;

    @Column(name = "gateway_access_cd" , nullable = false , length = 100)
    private String gatewayAccessCd;

    @Column(name = "bin_file_CRLF_ind")
    private Byte binFileCRLFind;

    @OneToOne
    @JoinColumn(name = "file_trns_id", referencedColumnName = "report_id")
    private AdminQueryList adminQueryList;

    public void setAdminQueryList(AdminQueryList adminQueryList) {
        this.adminQueryList = adminQueryList;
    }

    public AdminQueryList getAdminQueryList() {
        return adminQueryList;
    }

    public void setFileTransId(Integer fileTransId) {
        this.fileTransId = fileTransId;
    }

    public Integer getFileTransId() {
        return fileTransId;
    }

    public void setSequenceNr(Integer sequenceNr) {
        this.sequenceNr = sequenceNr;
    }

    public Integer getSequenceNr() {
        return sequenceNr;
    }

    public void setTransferCd(Integer transferCd) {
        this.transferCd = transferCd;
    }

    public Integer getTransferCd() {
        return transferCd;
    }

    public void setProtocolNm(String protocolNm) {
        this.protocolNm = protocolNm;
    }

    public String getProtocolNm() {
        return protocolNm;
    }

    public void setTransPrgNm(String transPrgNm) {
        this.transPrgNm = transPrgNm;
    }

    public String getTransPrgNm() {
        return transPrgNm;
    }

    public void setModeNm(String modeNm) {
        this.modeNm = modeNm;
    }
    public String getModeNm() {
        return modeNm;
    }

    public void setSecurityNm(String securityNm) {
        this.securityNm = securityNm;
    }

    public String getSecurityNm() {
        return securityNm;
    }

    public void setIpPortCd(String ipPortCd) {
        this.ipPortCd = ipPortCd;
    }

    public String getIpPortCd() {
        return ipPortCd;
    }

    public void setListenerSrvNm(String listenerSrvNm) {
        this.listenerSrvNm = listenerSrvNm;
    }

    public String getListenerSrvNm() {
        return listenerSrvNm;
    }

    public void setDdNm(String ddNm) {
        this.ddNm = ddNm;
    }

    public String getDdNm() {
        return ddNm;
    }

    public void setOrgTypeCd(Integer orgTypeCd) {
        this.OrgTypeCd = orgTypeCd;
    }

    public Integer getOrgTypeCd() {
        return OrgTypeCd;
    }

    public void setMemberCd(String memberCd) {
        this.memberCd = memberCd;
    }

    public String getMemberCd() {
        return memberCd;
    }

    public void setRecordLengthNr(Integer recordLengthNr) {
        this.recordLgthNr = recordLengthNr;
    }

    public Integer getRecordLengthNr() {
        return recordLgthNr;
    }

    public void setBlockSizeNr(Integer blockSizeNr) {
        this.blockSizeNr = blockSizeNr;
    }

    public Integer getBlockSizeNr() {
        return blockSizeNr;
    }

    public void setConvertFileCd(Integer convertFileCd) {
        this.convertFileCd = convertFileCd;
    }

    public Integer getConvertFileCd() {
        return convertFileCd;
    }

    public void setXferFileNm(String xferFileNm) {
        this.xferFileNm = xferFileNm;

    }

    public String getXferFileNm() {
        return xferFileNm;
    }

    public void setJobNm(String jobNm) {
        this.jobNm = jobNm;
    }

    public String getJobNm() {
        return jobNm;
    }

    public void setProgramNm(String programNm) {
        this.programNm = programNm;
    }

    public String getProgramNm() {
        return programNm;
    }

    public void setRemoteFileNm(String remoteFileNm) {
        this.remoteFileNm = remoteFileNm;
    }

    public String getRemoteFileNm() {
        return remoteFileNm;
    }

    public void setLocalFileNm(String localFileNm) {
        this.localFileNm = localFileNm;
    }

    public String getLocalFileNm() {
        return localFileNm;
    }

    public void setControlFileNm(String controlFileNm) {
        this.controlFileNm = controlFileNm;
    }

    public String getControlFileNm() {
        return controlFileNm;
    }

    public void setGatewayAccessCd(String gatewayAccessCd) {
        this.gatewayAccessCd = gatewayAccessCd;
    }

    public String getGatewayAccessCd() {
        return gatewayAccessCd;
    }

    public void setBinFileCRLFind(Byte binFileCRLFind) {
        this.binFileCRLFind = binFileCRLFind;
    }

    public Byte getBinFileCRLFind()
    {
       return binFileCRLFind;
    }
}
