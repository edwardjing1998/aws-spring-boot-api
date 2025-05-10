package admin.dto;
import admin.model.AdminQueryList;
import admin.model.C3FileTransfer;


public class C3FileTransferDTO {

    private Integer fileTransId;

    private Integer sequenceNr;

    private Integer transferCd;

    private String protocolNm;

    private String transPrgNm;

    private String ipPortCd;

    private Integer blockSizeNr;

    private Integer convertFileCd;

    private String modeNm;

    private String securityNm;

    private String xferFileNm;

    private String ddNm;

    private String memberCd;

    private String jobNm;

    private String remoteFileNm;

    private String gatewayAccessCd;

    private String listenerSrvNm;

    private Integer orgTypeCd;

    private String programNm;

    private Byte binFileCRLFInf;

    private String controlFileNm;

    private Integer recordLengthNr;

    private String localFileNm;

    private Integer adminQueryListId;

    private AdminQueryList adminQueryList; // Reference only, no JPA annotation

    public AdminQueryList getAdminQueryList() { return adminQueryList; }

    public void setAdminQueryList(AdminQueryList adminQueryList) { this.adminQueryList = adminQueryList; }

    public C3FileTransferDTO()
    {

    }

    public C3FileTransferDTO(C3FileTransfer c3FileTransfer) {

        this.protocolNm = c3FileTransfer.getProtocolNm();
        this.sequenceNr = c3FileTransfer.getSequenceNr();
        this.transferCd = c3FileTransfer.getTransferCd();
        this.fileTransId = c3FileTransfer.getFileTransId();
        this.transPrgNm = c3FileTransfer.getTransPrgNm();
        this.modeNm = c3FileTransfer.getModeNm();
        this.securityNm = c3FileTransfer.getSecurityNm();
        this.ipPortCd = c3FileTransfer.getIpPortCd();
        this.listenerSrvNm = c3FileTransfer.getListenerSrvNm();
        this.ddNm = c3FileTransfer.getDdNm();
        this.orgTypeCd = c3FileTransfer.getOrgTypeCd();
        this.memberCd = c3FileTransfer.getMemberCd();
        this.recordLengthNr = c3FileTransfer.getRecordLengthNr();
        this.blockSizeNr = c3FileTransfer.getBlockSizeNr();
        this.convertFileCd = c3FileTransfer.getConvertFileCd();
        this.xferFileNm = c3FileTransfer.getXferFileNm();
        this.jobNm = c3FileTransfer.getJobNm();
        this.remoteFileNm = c3FileTransfer.getRemoteFileNm();
        this.localFileNm = c3FileTransfer.getLocalFileNm();
        this.controlFileNm = c3FileTransfer.getControlFileNm();
        this.gatewayAccessCd = c3FileTransfer.getGatewayAccessCd();
        this.binFileCRLFInf = c3FileTransfer.getBinFileCRLFind();
    }


    public void setFileTransId(Integer fileTransId) {
        this.fileTransId = fileTransId;
    }

    public Integer getFileTransId() {
        return fileTransId;
    }

    public Integer getSequenceNr() {
        return sequenceNr;
    }

    public void setSequenceNr(Integer sequenceNr) {
        this.sequenceNr = sequenceNr;
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

    public void setDdm(String ddm) {
        this.ddNm = ddm;
    }

    public String getDdNm() {
        return ddNm;
    }

    public void setOrgTypeCd(Integer orgTypeCd) {
        this.orgTypeCd = orgTypeCd;
    }

    public Integer getOrgTypeCd() {
        return orgTypeCd;
    }

    public void setMemberCd(String memberCd) {
        this.memberCd = memberCd;
    }

    public String getMemberCd() {
        return memberCd;
    }

    public void setRecordLengthNr(Integer recordLengthNr) {
        this.recordLengthNr = recordLengthNr;
    }

    public Integer getRecordLengthNr() {
        return recordLengthNr;
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

    public void setXferFileNm(String XferFileNm) {
        this.xferFileNm = XferFileNm;
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

    public void setLocalFileNm(String localFileNm)
    {
        this.localFileNm = localFileNm;
    }

    public String getLocalFileNm()
    {
        return localFileNm;
    }

    public void setControlFileNm(String controlFileNm)
    {
        this.controlFileNm = controlFileNm;
    }

    public String getControlFileNm()
    {
        return controlFileNm;
    }

    public void setGatewayAccessCd(String gatewayAccessCd) {
        this.gatewayAccessCd = gatewayAccessCd;
    }

    public String getGatewayAccessCd()
    {
        return gatewayAccessCd;
    }

    public void setBinFileCRLFInf(Byte binFileCRLFInf) {
        this.binFileCRLFInf = binFileCRLFInf;
    }

    public Byte getBinFileCRLFInf() { return binFileCRLFInf; }
}
