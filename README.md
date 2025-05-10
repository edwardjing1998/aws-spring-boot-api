package admin.model;

import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.Id;
import jakarta.persistence.Table;

@Entity
@Table(name = "file_load_dest")
public class AdminTableLoad {

    @Id
    @Column(name = "report_id")
    private Integer reportId;

    @Column(name = "db_server")
    private String dbServer;

    @Column(name = "db_driver")
    private String dbDriver;

    @Column(name = "data_base")
    private String database;

    @Column(name = "user_id")
    private String userId;

    @Column(name = "pass_word")
    private String passWord;

    @Column(name = "db_ip_and_port")
    private String dbIpAndPort;

    @Column(name = "table_name")
    private String tableName;

    public AdminTableLoad() {
    }

     public Integer getReportId() {
        return reportId;
     }

     public void setReportId(Integer reportId) {
        this.reportId = reportId;
     }

     public String getDbServer() {
        return dbServer;
     }

     public void setDbServer(String dbServer) {
        this.dbServer = dbServer;
     }

     public String getDbDriver() {
        return dbDriver;
     }

     public void setDbDriver(String dbDriver) {
        this.dbDriver = dbDriver;
     }

     public String getDatabase() {
        return database;
     }

     public void setDatabase(String database) {
        this.database = database;
     }

     public String getUserId() {
        return userId;
     }

     public void setUserId(String userId) {
        this.userId = userId;
     }

     public String getPassWord() {
        return passWord;
     }

     public void setPassWord(String passWord) {
        this.passWord = passWord;
     }

     public String getIbAndPort() {
        return dbIpAndPort;
     }

     public void setDbIpAndPort(String dbIpAndPort) {
        this.dbIpAndPort = dbIpAndPort;
     }

     public String getTableName() {
        return tableName;
     }

     public void setTableName(String tableName) {
        this.tableName = tableName;
     }
}
