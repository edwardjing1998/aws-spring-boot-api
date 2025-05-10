package admin.dto;

import admin.model.AdminTableLoad;


public class AdminTableLoadDTO {


    private Integer reportId;

    private String dbServer;

    private String dbDriver;

    private String database;

    private String userId;

    private String passWord;

    private String dbIpAndPort;

    private String tableName;

    public AdminTableLoadDTO() {

    }

    public  AdminTableLoadDTO(AdminTableLoad adminTableLoad) {
        this.reportId = adminTableLoad.getReportId();
        this.dbServer = adminTableLoad.getDbServer();
        this.dbDriver = adminTableLoad.getDbDriver();
        this.database = adminTableLoad.getDatabase();
        this.userId = adminTableLoad.getUserId();
        this.passWord = adminTableLoad.getPassWord();
        this.dbIpAndPort = adminTableLoad.getIbAndPort();
        this.tableName = adminTableLoad.getTableName();
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

    public String getDbIpAndPort() {
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
