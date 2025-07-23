package admin.dto;


import admin.model.AdminQueryToolTips;

public class AdminQueryToolTipsDTO {

    private Integer reportId;

    private String tool_tip0_tx;

    private String tool_tip1_tx;

    private String tool_tip2_tx;

    private String tool_tip3_tx;

    private String tool_tip4_tx;

    private String tool_tip5_tx;

    private String tool_tip6_tx;

    private String tool_tip7_tx;

    private String tool_tip8_tx;

    public AdminQueryToolTipsDTO() {

    }

    public AdminQueryToolTipsDTO(AdminQueryToolTips adminQueryToolTips) {
        this.reportId = adminQueryToolTips.getReportId();
        this.tool_tip0_tx = adminQueryToolTips.getTool_tip0_tx();
        this.tool_tip1_tx = adminQueryToolTips.getTool_tip1_tx();
        this.tool_tip2_tx = adminQueryToolTips.getTool_tip2_tx();
        this.tool_tip3_tx = adminQueryToolTips.getTool_tip3_tx();
        this.tool_tip4_tx = adminQueryToolTips.getTool_tip4_tx();
        this.tool_tip5_tx = adminQueryToolTips.getTool_tip5_tx();
        this.tool_tip6_tx = adminQueryToolTips.getTool_tip6_tx();
        this.tool_tip7_tx = adminQueryToolTips.getTool_tip7_tx();
        this.tool_tip8_tx = adminQueryToolTips.getTool_tip8_tx();
    }

    public Integer getReportId() {
        return reportId;
    }

    public void setReportId(Integer reportId) {
        this.reportId = reportId;
    }

    public String getTool_tip0_tx() {
        return tool_tip0_tx;
    }

    public void setTool_tip0_tx(String tool_tip0_tx) {
        this.tool_tip0_tx = tool_tip0_tx;
    }
    public String getTool_tip1_tx() {
        return tool_tip1_tx;
    }

    public void setTool_tip1_tx(String tool_tip1_tx) {
        this.tool_tip1_tx = tool_tip1_tx;
    }

    public String getTool_tip2_tx() {
        return tool_tip2_tx;
    }

    public void setTool_tip2_tx(String tool_tip2_tx) {
        this.tool_tip2_tx = tool_tip2_tx;
    }
    public String getTool_tip3_tx() {
        return tool_tip3_tx;
    }

    public void setTool_tip3_tx(String tool_tip3_tx) {
        this.tool_tip3_tx = tool_tip3_tx;
    }
    public String getTool_tip4_tx() {
        return tool_tip4_tx;
    }

    public void setTool_tip4_tx(String tool_tip4_tx) {
        this.tool_tip4_tx = tool_tip4_tx;
    }

    public String getTool_tip5_tx() {
        return tool_tip5_tx;
    }
    public void setTool_tip5_tx(String tool_tip5_tx) {
        this.tool_tip5_tx = tool_tip5_tx;
    }

    public String getTool_tip6_tx() {
        return tool_tip6_tx;
    }

    public void setTool_tip6_tx(String tool_tip6_tx) {
        this.tool_tip6_tx = tool_tip6_tx;
    }

    public String getTool_tip7_tx() {
        return tool_tip7_tx;
    }

    public void setTool_tip7_tx(String tool_tip7_tx) {
        this.tool_tip7_tx = tool_tip7_tx;
    }

    public String getTool_tip8_tx() {
        return tool_tip8_tx;
    }

    public void setTool_tip8_tx(String tool_tip8_tx) {
        this.tool_tip8_tx = tool_tip8_tx;
    }


}
