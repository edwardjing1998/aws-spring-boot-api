package admin.model;

import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.Id;
import jakarta.persistence.Table;

@Entity
@Table(name = "admin_query_tool_tips")
public class AdminQueryToolTips {

    @Id
    @Column(name = "report_id")
    private Integer reportId;

    @Column(name = "tool_tip0_tx", nullable = true, length = 50)
    private String tool_tip0_tx;

    @Column(name = "tool_tip1_tx", nullable = true, length = 50)
    private String tool_tip1_tx;

    @Column(name = "tool_tip2_tx", nullable = true, length = 50)
    private String tool_tip2_tx;

    @Column(name = "tool_tip3_tx", nullable = true, length = 50)
    private String tool_tip3_tx;

    @Column(name = "tool_tip4_tx", nullable = true, length = 50)
    private String tool_tip4_tx;

    @Column(name = "tool_tip5_tx", nullable = true, length = 50)
    private String tool_tip5_tx;

    @Column(name = "tool_tip6_tx", nullable = true, length = 50)
    private String tool_tip6_tx;

    @Column(name = "tool_tip7_tx", nullable = true, length = 50)
    private String tool_tip7_tx;

    @Column(name = "tool_tip8_tx", nullable = true, length = 50)
    private String tool_tip8_tx;

    public AdminQueryToolTips() {

    }

    public AdminQueryToolTips(Integer reportId,String tool_tip0_tx,String tool_tip1_tx,
                              String tool_tip2_tx,String tool_tip3_tx,String tool_tip4_tx, String tool_tip5_tx, String tool_tip6_tx,String tool_tip7_tx,String tool_tip8_tx) {
        this.reportId = reportId;
        this.tool_tip0_tx = tool_tip0_tx;
        this.tool_tip1_tx = tool_tip1_tx;
        this.tool_tip2_tx = tool_tip2_tx;
        this.tool_tip3_tx = tool_tip3_tx;
        this.tool_tip4_tx = tool_tip4_tx;
        this.tool_tip5_tx = tool_tip5_tx;
        this.tool_tip6_tx = tool_tip6_tx;
        this.tool_tip7_tx = tool_tip7_tx;
        this.tool_tip8_tx = tool_tip8_tx;
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




package admin.service;

import admin.dto.AdminQueryToolTipsDTO;
import admin.model.AdminQueryList;
import admin.model.AdminQueryToolTips;
import admin.model.C3FileTransfer;
import admin.repository.AdminQueryToolTipsRepository;
import admin.repository.AdminTableLoadColumnMappingRepository;
import org.springframework.stereotype.Service;

import java.util.ArrayList;
import java.util.List;
import java.util.Optional;

@Service
public class AdminQueryToolTipsService {

    private final AdminQueryToolTipsRepository adminQueryToolTipsRepository;

    public AdminQueryToolTipsService(AdminQueryToolTipsRepository adminQueryToolTipsRepository) {
        this.adminQueryToolTipsRepository = adminQueryToolTipsRepository;
    }

    public List<AdminQueryToolTips> getallTooltipsList() {
      return adminQueryToolTipsRepository.findAll();
    }

    public AdminQueryToolTipsDTO getallTooltipsListbyReportId(Integer reportId) {

        Optional<AdminQueryToolTips> AdminQuerytooltip = adminQueryToolTipsRepository.findById(reportId);
        return AdminQuerytooltip.map(AdminQueryToolTipsDTO::new).orElse(null);
    }

    public AdminQueryToolTips createAdminQueryToolTipList(AdminQueryToolTips adminQueryToolTips) {
        return adminQueryToolTipsRepository.save(adminQueryToolTips);
    }

    public boolean deleteAdminQueryToolTipList(Integer reportId) {

        Optional<AdminQueryToolTips>  adminQueryToolTips = adminQueryToolTipsRepository.findById(reportId);

        if (adminQueryToolTips.isPresent()) {
            AdminQueryToolTips adminQueryToolTipsCurrent = adminQueryToolTips.get();
            adminQueryToolTipsRepository.delete(adminQueryToolTipsCurrent);
            return true;
        }
        return false;
    }

    public Optional<AdminQueryToolTips> updateAdminQueryToolTipsList(AdminQueryToolTipsDTO adminQueryToolTipsDTO) {
        var existingAdminQueryTooltips = adminQueryToolTipsRepository.findById(adminQueryToolTipsDTO.getReportId());

        if (existingAdminQueryTooltips.isPresent()) {
            AdminQueryToolTips updated = existingAdminQueryTooltips.get();
            updated.setReportId(adminQueryToolTipsDTO.getReportId());
            updated.setTool_tip0_tx(adminQueryToolTipsDTO.getTool_tip0_tx());
            updated.setTool_tip1_tx(adminQueryToolTipsDTO.getTool_tip1_tx());
            updated.setTool_tip2_tx(adminQueryToolTipsDTO.getTool_tip2_tx());
            updated.setTool_tip3_tx(adminQueryToolTipsDTO.getTool_tip3_tx());
            updated.setTool_tip4_tx(adminQueryToolTipsDTO.getTool_tip4_tx());
            updated.setTool_tip5_tx(adminQueryToolTipsDTO.getTool_tip5_tx());
            updated.setTool_tip6_tx(adminQueryToolTipsDTO.getTool_tip6_tx());
            updated.setTool_tip7_tx(adminQueryToolTipsDTO.getTool_tip7_tx());
            updated.setTool_tip8_tx(adminQueryToolTipsDTO.getTool_tip8_tx());
            return Optional.of(adminQueryToolTipsRepository.save(updated));
        }

        return Optional.empty();
    }
}





