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


package admin.model;

import com.fasterxml.jackson.annotation.JsonIgnore;
import jakarta.persistence.*;
import java.time.LocalDateTime;

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

    public AdminQueryList() {
        
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






@ManyToOne(fetch = FetchType.LAZY)
@JoinColumn(name = "REPORT_ID", insertable = false, updatable = false)
private AdminQueryList report;



@OneToMany(mappedBy = "report", cascade = CascadeType.ALL, orphanRemoval = true)
private List<ClientReportOption> clientReportOptions = new ArrayList<>();






