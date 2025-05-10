package admin.model;

import com.fasterxml.jackson.annotation.JsonIgnore;
import jakarta.persistence.*;
import java.time.LocalDateTime;
import java.util.ArrayList;
import java.util.List;

@Entity
@Table(name = "ADMIN_QUERY_LIST")
public class AdminQueryList {

    @Id
    @Column(name = "report_id")
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Integer reportId;

    @Column(name = "query_name", nullable = false, length = 50)
    private String queryName;

    @Column(name = "query", columnDefinition = "TEXT")
    private String query;

    @Column(name = "input_data_fields", nullable = false, length = 255)
    private String inputDataFields;

    @Column(name = "file_ext", nullable = false, length = 3)
    private String fileExt;

    @Column(name = "db_driver_type", nullable = false, length = 30)
    private String dbDriverType;

    @Column(name = "file_header_ind", nullable = false)
    private Integer fileHeaderInd;

    @Column(name = "default_file_nm", nullable = false, length = 100)
    private String defaultFileNm;

    @Column(name = "report_db_server", nullable = false, length = 100)
    private String reportDbServer;

    @Column(name = "report_db", nullable = false, length = 100)
    private String reportDb;

    @Column(name = "report_db_userid", nullable = false, length = 50)
    private String reportDbUserid;

    @Column(name = "report_db_passwrd", nullable = false, length = 100)
    private String reportDbPasswrd;

    @Column(name = "file_transfer_type")
    private Integer fileTransferType;

    @Column(name = "report_db_ip_and_port", nullable = false, length = 100)
    private String reportDbIpAndPort;

    @Column(name = "report_by_client_flag")
    private Boolean reportByClientFlag;

    @Column(name = "rerun_date_range_start")
    private LocalDateTime rerunDateRangeStart;

    @Column(name = "rerun_date_range_end")
    private LocalDateTime rerunDateRangeEnd;

    @Column(name = "rerun_client_id", length = 4)
    private String rerunClientId;

    @Column(name = "email_from_address", length = 50)
    private String emailFromAddress;

    @Column(name = "email_event_id", length = 50)
    private String emailEventId;

    @Column(name = "tab_delimited_flag")
    private Boolean tabDelimitedFlag;

    @Column(name = "input_file_tx", length = 100)
    private String inputFileTx;

    @Column(name = "input_file_key_start_pos")
    private Integer inputFileKeyStartPos;

    @Column(name = "input_file_key_length")
    private Integer inputFileKeyLength;

    @Column(name = "access_level")
    private Byte accessLevel;

    @Column(name = "is_active")
    private Boolean isActive;

    @Column(name = "is_visible")
    private Boolean isVisible;

    @Column(name = "num_sheets")
    private Integer numSheets;

    @OneToOne(mappedBy = "adminQueryList", cascade = CascadeType.ALL, fetch = FetchType.LAZY, optional = false)
    @JsonIgnore
    private C3FileTransfer c3FileTransfer;

    @OneToMany(mappedBy = "report", cascade = CascadeType.ALL, orphanRemoval = true)
    private List<ClientReportOption> clientReportOptions = new ArrayList<>();

    public AdminQueryList() {
        
    }

    public List<ClientReportOption>  getClientReportOptions() {
        return clientReportOptions;
    }

    public void setClientReportOptions(List<ClientReportOption> clientReportOptions) {
        this.clientReportOptions = clientReportOptions;
    }

    // ------ Getter and Setter Methods ------
    public C3FileTransfer getC3FileTransfer() {
        return c3FileTransfer;
    }

    public void setC3FileTransfer(C3FileTransfer c3FileTransfer) {
        this.c3FileTransfer = c3FileTransfer;
    }

    public Integer getReportId() {
        return reportId;
    }

    public String getQueryName() {
        return queryName;
    }

    public void setQueryName(String queryName) {
        this.queryName = queryName;
    }

    public String getQuery() {
        return query;
    }

    public void setQuery(String query) {
        this.query = query;
    }

    public String getInputDataFields() {
        return inputDataFields;
    }

    public void setInputDataFields(String inputDataFields) {
        this.inputDataFields = inputDataFields;
    }

    public String getFileExt() {
        return fileExt;
    }

    public void setFileExt(String fileExt) {
        this.fileExt = fileExt;
    }

    public String getDbDriverType() {
        return dbDriverType;
    }

    public void setDbDriverType(String dbDriverType) {
        this.dbDriverType = dbDriverType;
    }

    public Integer getFileHeaderInd() {
        return fileHeaderInd;
    }

    public void setFileHeaderInd(Integer fileHeaderInd) {
        this.fileHeaderInd = fileHeaderInd;
    }

    public String getDefaultFileNm() {
        return defaultFileNm;
    }

    public void setDefaultFileNm(String defaultFileNm) {
        this.defaultFileNm = defaultFileNm;
    }

    public String getReportDbServer() {
        return reportDbServer;
    }

    public void setReportDbServer(String reportDbServer) {
        this.reportDbServer = reportDbServer;
    }

    public String getReportDb() {
        return reportDb;
    }

    public void setReportDb(String reportDb) {
        this.reportDb = reportDb;
    }

    public String getReportDbUserid() {
        return reportDbUserid;
    }

    public void setReportDbUserid(String reportDbUserid) {
        this.reportDbUserid = reportDbUserid;
    }

    public String getReportDbPasswrd() {
        return reportDbPasswrd;
    }

    public void setReportDbPasswrd(String reportDbPasswrd) {
        this.reportDbPasswrd = reportDbPasswrd;
    }

    public Integer getFileTransferType() {
        return fileTransferType;
    }

    public void setFileTransferType(Integer fileTransferType) {
        this.fileTransferType = fileTransferType;
    }

    public String getReportDbIpAndPort() {
        return reportDbIpAndPort;
    }

    public void setReportDbIpAndPort(String reportDbIpAndPort) {
        this.reportDbIpAndPort = reportDbIpAndPort;
    }

    public Boolean getReportByClientFlag() {
        return reportByClientFlag;
    }

    public void setReportByClientFlag(Boolean reportByClientFlag) {
        this.reportByClientFlag = reportByClientFlag;
    }

    public LocalDateTime getRerunDateRangeStart() {
        return rerunDateRangeStart;
    }

    public void setRerunDateRangeStart(LocalDateTime rerunDateRangeStart) {
        this.rerunDateRangeStart = rerunDateRangeStart;
    }

    public LocalDateTime getRerunDateRangeEnd() {
        return rerunDateRangeEnd;
    }

    public void setRerunDateRangeEnd(LocalDateTime rerunDateRangeEnd) {
        this.rerunDateRangeEnd = rerunDateRangeEnd;
    }

    public String getRerunClientId() {
        return rerunClientId;
    }

    public void setRerunClientId(String rerunClientId) {
        this.rerunClientId = rerunClientId;
    }

    public String getEmailFromAddress() {
        return emailFromAddress;
    }

    public void setEmailFromAddress(String emailFromAddress) {
        this.emailFromAddress = emailFromAddress;
    }

    public String getEmailEventId() {
        return emailEventId;
    }

    public void setEmailEventId(String emailEventId) {
        this.emailEventId = emailEventId;
    }

    public Boolean getTabDelimitedFlag() {
        return tabDelimitedFlag;
    }

    public void setTabDelimitedFlag(Boolean tabDelimitedFlag) {
        this.tabDelimitedFlag = tabDelimitedFlag;
    }

    public String getInputFileTx() {
        return inputFileTx;
    }

    public void setInputFileTx(String inputFileTx) {
        this.inputFileTx = inputFileTx;
    }

    public Integer getInputFileKeyStartPos() {
        return inputFileKeyStartPos;
    }

    public void setInputFileKeyStartPos(Integer inputFileKeyStartPos) {
        this.inputFileKeyStartPos = inputFileKeyStartPos;
    }

    public Integer getInputFileKeyLength() {
        return inputFileKeyLength;
    }

    public void setInputFileKeyLength(Integer inputFileKeyLength) {
        this.inputFileKeyLength = inputFileKeyLength;
    }

    public Byte getAccessLevel() {
        return accessLevel;
    }

    public void setAccessLevel(Byte accessLevel) {
        this.accessLevel = accessLevel;
    }

    public Boolean getIsActive() {
        return isActive;
    }

    public void setIsActive(Boolean isActive) {
        this.isActive = isActive;
    }

    public Boolean getIsVisible() {
        return isVisible;
    }

    public void setIsVisible(Boolean isVisible) {
        this.isVisible = isVisible;
    }

    public Integer getNumSheets() {
        return numSheets;
    }

    public void setNumSheets(Integer numSheets) {
        this.numSheets = numSheets;
    }
}




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






package admin.model;

import jakarta.persistence.*;
import org.hibernate.annotations.Where;

import java.util.Objects;

@Entity
@Table(name = "CLIENT_REPORT_OPTIONS")
public class ClientReportOption {

    @EmbeddedId
    private ClientReportOptionId id = new ClientReportOptionId();

    @Column(name = "RECEIVE_FLAG", nullable = false)
    private Boolean receiveFlag;

    @Column(name = "OUTPUT_TYPE_CD", nullable = false)
    private Integer outputTypeCd;

    @Column(name = "FILE_TYPE_CD", nullable = false)
    private Integer fileTypeCd;

    @Column(name = "EMAIL_FLAG", nullable = false)
    private Integer emailFlag;

    @Column(name = "EMAIL_BODY_TX")
    private String emailBodyTx;

    @Column(name = "REPORT_PASSWORD_TX")
    private String reportPasswordTx;

    // ✅ New association with AdminQueryList

    @ManyToOne
    @JoinColumn(name = "REPORT_ID", referencedColumnName = "REPORT_ID", insertable = false, updatable = false)
    @Where(clause = "REPORT_BY_CLIENT_FLAG = 1")
    private AdminQueryList report;

    public ClientReportOption() {}

    public ClientReportOption(Boolean receiveFlag,
                              Integer outputTypeCd, Integer fileTypeCd, Integer emailFlag,
                              String reportPasswordTx, String emailBodyTx) {
        this.receiveFlag = receiveFlag;
        this.outputTypeCd = outputTypeCd;
        this.fileTypeCd = fileTypeCd;
        this.emailFlag = emailFlag;
        this.reportPasswordTx = reportPasswordTx;
        this.emailBodyTx = emailBodyTx;
    }

    public ClientReportOptionId getId() {
        return id;
    }

    public void setId(ClientReportOptionId id) {
        this.id = id;
    }

    public Boolean getReceiveFlag() {
        return receiveFlag;
    }

    public void setReceiveFlag(Boolean receiveFlag) {
        this.receiveFlag = receiveFlag;
    }

    public Integer getOutputTypeCd() {
        return outputTypeCd;
    }

    public void setOutputTypeCd(Integer outputTypeCd) {
        this.outputTypeCd = outputTypeCd;
    }

    public Integer getFileTypeCd() {
        return fileTypeCd;
    }

    public void setFileTypeCd(Integer fileTypeCd) {
        this.fileTypeCd = fileTypeCd;
    }

    public Integer getEmailFlag() {
        return emailFlag;
    }

    public void setEmailFlag(Integer emailFlag) {
        this.emailFlag = emailFlag;
    }

    public String getReportPasswordTx() {
        return reportPasswordTx;
    }

    public void setReportPasswordTx(String reportPasswordTx) {
        this.reportPasswordTx = reportPasswordTx;
    }

    public String getEmailBodyTx() {
        return emailBodyTx;
    }

    public void setEmailBodyTx(String emailBodyTx) {
        this.emailBodyTx = emailBodyTx;
    }


    public AdminQueryList getReport() {
        return report;
    }

    public void setReport(AdminQueryList report) {
        this.report = report;
    }

    // equals, hashCode, and toString
    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof ClientReportOption)) return false;
        ClientReportOption that = (ClientReportOption) o;
        return Objects.equals(id, that.id);
    }

    @Override
    public int hashCode() {
        return Objects.hash(id);
    }

    @Override
    public String toString() {
        return "ClientReportOption{" +
                "id=" + id +
                ", receiveFlag=" + receiveFlag +
                ", outputTypeCd=" + outputTypeCd +
                ", fileTypeCd=" + fileTypeCd +
                ", emailFlag=" + emailFlag +
                ", reportPasswordTx='" + reportPasswordTx + '\'' +
                '}';
    }
}






package admin.controller;

import admin.dto.AdminQueryListDTO;
import admin.model.AdminQueryList;
import admin.service.AdminQueryListService;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/api/admin-query-list")
@CrossOrigin(origins = "http://localhost:3000")
public class AdminQueryListController {

    private final AdminQueryListService adminQueryListService;

    // Constructor for dependency injection
    public AdminQueryListController(AdminQueryListService adminQueryListService) {
        this.adminQueryListService = adminQueryListService;
    }

    @GetMapping("/all")
    public ResponseEntity<List<AdminQueryList>> getAllAdminQueryList(){
        List<AdminQueryList>  adminQueryLists = adminQueryListService.getAllAdminQueryList();
        return ResponseEntity.ok(adminQueryLists);
    }

    @GetMapping("/fileTransferType/{fileTranferType}")
    public ResponseEntity<List<AdminQueryList>> getAllAdminQueryListbyFileTransferType(Integer fileTransferType) {
        List<AdminQueryList> adminQueryLists = adminQueryListService.getAdminQueryListbyFileTransferType(fileTransferType);
        return ResponseEntity.ok(adminQueryLists);
    }

    @GetMapping("/{reportId}")
    public ResponseEntity<AdminQueryListDTO> getAdminQueryListbyReportId(@PathVariable Integer reportId) {

        if(reportId != null) {
            AdminQueryListDTO adminQueryListDTO = adminQueryListService.getAdminQueryListbyReportId(reportId);
            return ResponseEntity.ok(adminQueryListDTO);
        }
        else
        {
            return new ResponseEntity<>(HttpStatus.BAD_REQUEST);
        }
    }

    @PostMapping
    public ResponseEntity<AdminQueryList> createAdminQueryList(@RequestBody AdminQueryListDTO adminQueryList) {
        if(adminQueryList != null) {
            AdminQueryList adminQueryListObj = adminQueryListService.createAdminQueryList(adminQueryList);
            return ResponseEntity.status(HttpStatus.CREATED).body(adminQueryListObj);
        }
        else {
            return new ResponseEntity<>(HttpStatus.BAD_REQUEST);
        }
    }

    @DeleteMapping("/{reportId}")
    public ResponseEntity<String> deleteAdminQueryList(@PathVariable Integer reportId) {

            if(reportId != null) {
                boolean deleted = adminQueryListService.deleteAdminQueryList(reportId);

                if (deleted) {
                    return ResponseEntity.ok("Admin Query List deleted successfully.");
                } else {
                    return ResponseEntity.notFound().build();
                }
            }

        return new ResponseEntity<>(HttpStatus.BAD_REQUEST);
    }

    @PutMapping("/{reportId}")
    public ResponseEntity<AdminQueryList> updateAdminQueryList(@PathVariable Integer reportId, @RequestBody AdminQueryListDTO AdminQueryListDTO) {

        if(reportId != null && AdminQueryListDTO != null) {
          var currentAdminQueryListObj = adminQueryListService.updateAdminQueryList(reportId, AdminQueryListDTO);
          if(currentAdminQueryListObj.isPresent()) {
              return ResponseEntity.ok(currentAdminQueryListObj.get());
          }
        }
        return new ResponseEntity<>(HttpStatus.BAD_REQUEST);
    }
}








package admin.dto;

import admin.model.AdminQueryList;

import java.time.LocalDateTime;

public class AdminQueryListDTO {

    public AdminQueryListDTO(AdminQueryList entity) {
        this.reportId = entity.getReportId();
        this.queryName = entity.getQueryName();
        this.query = entity.getQuery();
        this.inputDataFields = entity.getInputDataFields();
        this.fileExt = entity.getFileExt();
        this.dbDriverType = entity.getDbDriverType();
        this.fileHeaderInd = entity.getFileHeaderInd();
        this.defaultFileNm = entity.getDefaultFileNm();
        this.reportDbServer = entity.getReportDbServer();
        this.reportDb = entity.getReportDb();
        this.reportDbUserid = entity.getReportDbUserid();
        this.reportDbPasswrd = entity.getReportDbPasswrd();
        this.fileTransferType = entity.getFileTransferType();
        this.reportDbIpAndPort = entity.getReportDbIpAndPort();
        this.reportByClientFlag = entity.getReportByClientFlag();
        this.rerunDateRangeStart = entity.getRerunDateRangeStart();
        this.rerunDateRangeEnd = entity.getRerunDateRangeEnd();
        this.rerunClientId = entity.getRerunClientId();
        this.emailFromAddress = entity.getEmailFromAddress();
        this.emailEventId = entity.getEmailEventId();
        this.tabDelimitedFlag = entity.getTabDelimitedFlag();
        this.inputFileTx = entity.getInputFileTx();
        this.inputFileKeyStartPos = entity.getInputFileKeyStartPos();
        this.inputFileKeyLength = entity.getInputFileKeyLength();
        this.accessLevel = entity.getAccessLevel();
        this.isActive = entity.getIsActive();
        this.isVisible = entity.getIsVisible();
        this.numSheets = entity.getNumSheets();
    }


    private Integer reportId;
    private String queryName;
    private String query;
    private String inputDataFields;
    private String fileExt;
    private String dbDriverType;
    private Integer fileHeaderInd;
    private String defaultFileNm;
    private String reportDbServer;
    private String reportDb;
    private String reportDbUserid;
    private String reportDbPasswrd;
    private Integer fileTransferType;
    private String reportDbIpAndPort;
    private Boolean reportByClientFlag;
    private LocalDateTime rerunDateRangeStart;
    private LocalDateTime rerunDateRangeEnd;
    private String rerunClientId;
    private String emailFromAddress;
    private String emailEventId;
    private Boolean tabDelimitedFlag;
    private String inputFileTx;
    private Integer inputFileKeyStartPos;
    private Integer inputFileKeyLength;
    private Byte accessLevel;
    private Boolean isActive;
    private Boolean isVisible;
    private Integer numSheets;

    public AdminQueryListDTO() {
    }

    public Integer getReportId() {
        return reportId;
    }

    public void setReportId(Integer reportId) {
        this.reportId = reportId;
    }

    public String getQueryName() {
        return queryName;
    }

    public void setQueryName(String queryName) {
        this.queryName = queryName;
    }

    public String getQuery() {
        return query;
    }

    public void setQuery(String query) {
        this.query = query;
    }

    public String getInputDataFields() {
        return inputDataFields;
    }

    public void setInputDataFields(String inputDataFields) {
        this.inputDataFields = inputDataFields;
    }

    public String getFileExt() {
        return fileExt;
    }

    public void setFileExt(String fileExt) {
        this.fileExt = fileExt;
    }

    public String getDbDriverType() {
        return dbDriverType;
    }

    public void setDbDriverType(String dbDriverType) {
        this.dbDriverType = dbDriverType;
    }

    public Integer getFileHeaderInd() {
        return fileHeaderInd;
    }

    public void setFileHeaderInd(Integer fileHeaderInd) {
        this.fileHeaderInd = fileHeaderInd;
    }

    public String getDefaultFileNm() {
        return defaultFileNm;
    }

    public void setDefaultFileNm(String defaultFileNm) {
        this.defaultFileNm = defaultFileNm;
    }

    public String getReportDbServer() {
        return reportDbServer;
    }

    public void setReportDbServer(String reportDbServer) {
        this.reportDbServer = reportDbServer;
    }

    public String getReportDb() {
        return reportDb;
    }

    public void setReportDb(String reportDb) {
        this.reportDb = reportDb;
    }

    public String getReportDbUserid() {
        return reportDbUserid;
    }

    public void setReportDbUserid(String reportDbUserid) {
        this.reportDbUserid = reportDbUserid;
    }

    public String getReportDbPasswrd() {
        return reportDbPasswrd;
    }

    public void setReportDbPasswrd(String reportDbPasswrd) {
        this.reportDbPasswrd = reportDbPasswrd;
    }

    public Integer getFileTransferType() {
        return fileTransferType;
    }

    public void setFileTransferType(Integer fileTransferType) {
        this.fileTransferType = fileTransferType;
    }

    public String getReportDbIpAndPort() {
        return reportDbIpAndPort;
    }

    public void setReportDbIpAndPort(String reportDbIpAndPort) {
        this.reportDbIpAndPort = reportDbIpAndPort;
    }

    public Boolean getReportByClientFlag() {
        return reportByClientFlag;
    }

    public void setReportByClientFlag(Boolean reportByClientFlag) {
        this.reportByClientFlag = reportByClientFlag;
    }

    public LocalDateTime getRerunDateRangeStart() {
        return rerunDateRangeStart;
    }

    public void setRerunDateRangeStart(LocalDateTime rerunDateRangeStart) {
        this.rerunDateRangeStart = rerunDateRangeStart;
    }

    public LocalDateTime getRerunDateRangeEnd() {
        return rerunDateRangeEnd;
    }

    public void setRerunDateRangeEnd(LocalDateTime rerunDateRangeEnd) {
        this.rerunDateRangeEnd = rerunDateRangeEnd;
    }

    public String getRerunClientId() {
        return rerunClientId;
    }

    public void setRerunClientId(String rerunClientId) {
        this.rerunClientId = rerunClientId;
    }

    public String getEmailFromAddress() {
        return emailFromAddress;
    }

    public void setEmailFromAddress(String emailFromAddress) {
        this.emailFromAddress = emailFromAddress;
    }

    public String getEmailEventId() {
        return emailEventId;
    }

    public void setEmailEventId(String emailEventId) {
        this.emailEventId = emailEventId;
    }

    public Boolean getTabDelimitedFlag() {
        return tabDelimitedFlag;
    }

    public void setTabDelimitedFlag(Boolean tabDelimitedFlag) {
        this.tabDelimitedFlag = tabDelimitedFlag;
    }

    public String getInputFileTx() {
        return inputFileTx;
    }

    public void setInputFileTx(String inputFileTx) {
        this.inputFileTx = inputFileTx;
    }

    public Integer getInputFileKeyStartPos() {
        return inputFileKeyStartPos;
    }

    public void setInputFileKeyStartPos(Integer inputFileKeyStartPos) {
        this.inputFileKeyStartPos = inputFileKeyStartPos;
    }

    public Integer getInputFileKeyLength() {
        return inputFileKeyLength;
    }

    public void setInputFileKeyLength(Integer inputFileKeyLength) {
        this.inputFileKeyLength = inputFileKeyLength;
    }

    public Byte getAccessLevel() {
        return accessLevel;
    }

    public void setAccessLevel(Byte accessLevel) {
        this.accessLevel = accessLevel;
    }

    public Boolean getIsActive() {
        return isActive;
    }

    public void setIsActive(Boolean isActive) {
        this.isActive = isActive;
    }

    public Boolean getIsVisible() {
        return isVisible;
    }

    public void setIsVisible(Boolean isVisible) {
        this.isVisible = isVisible;
    }

    public Integer getNumSheets() {
        return numSheets;
    }

    public void setNumSheets(Integer numSheets) {
        this.numSheets = numSheets;
    }
}







@PostMapping("/new")
public ResponseEntity<AdminQueryList> createIndependentReport(@RequestBody AdminQueryListDTO dto) {
    AdminQueryList saved = service.insertIndependentRecordFromDTO(dto);
    return ResponseEntity.ok(saved);
}



public AdminQueryList insertIndependentRecordFromDTO(AdminQueryListDTO dto) {
    AdminQueryList entity = new AdminQueryList();

    entity.setQueryName(dto.getQueryName());
    entity.setQuery(dto.getQuery());
    entity.setInputDataFields(dto.getInputDataFields());
    entity.setFileExt(dto.getFileExt());
    entity.setDbDriverType(dto.getDbDriverType());
    entity.setFileHeaderInd(dto.getFileHeaderInd());
    entity.setDefaultFileNm(dto.getDefaultFileNm());
    entity.setReportDbServer(dto.getReportDbServer());
    entity.setReportDb(dto.getReportDb());
    entity.setReportDbUserid(dto.getReportDbUserid());
    entity.setReportDbPasswrd(dto.getReportDbPasswrd());
    entity.setFileTransferType(dto.getFileTransferType());
    entity.setReportDbIpAndPort(dto.getReportDbIpAndPort());
    entity.setReportByClientFlag(dto.getReportByClientFlag());
    entity.setRerunDateRangeStart(dto.getRerunDateRangeStart());
    entity.setRerunDateRangeEnd(dto.getRerunDateRangeEnd());
    entity.setRerunClientId(dto.getRerunClientId());
    entity.setEmailFromAddress(dto.getEmailFromAddress());
    entity.setEmailEventId(dto.getEmailEventId());
    entity.setTabDelimitedFlag(dto.getTabDelimitedFlag());
    entity.setInputFileTx(dto.getInputFileTx());
    entity.setInputFileKeyStartPos(dto.getInputFileKeyStartPos());
    entity.setInputFileKeyLength(dto.getInputFileKeyLength());
    entity.setAccessLevel(dto.getAccessLevel());
    entity.setIsActive(dto.getIsActive());
    entity.setIsVisible(dto.getIsVisible());
    entity.setNumSheets(dto.getNumSheets());

    return repository.save(entity);
}










