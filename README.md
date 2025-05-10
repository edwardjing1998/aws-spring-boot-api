package admin.service;

import admin.dto.AdminTableLoadDTO;
import admin.model.AdminTableLoad;
import admin.repository.AdminTableLoadRepository;
import jakarta.transaction.Transactional;
import org.springframework.stereotype.Service;

import java.util.List;
import java.util.Optional;

@Service
public class AdminTableLoadService {

    private final AdminTableLoadRepository  adminTableLoadRepository;

    public AdminTableLoadService(AdminTableLoadRepository  adminTableLoadRepository) {
        this.adminTableLoadRepository = adminTableLoadRepository;
    }

    public List<AdminTableLoad> getAllAdminTableLoadList() {
        return adminTableLoadRepository.findAll();
    }

    public AdminTableLoad createAdminTableLoadList(AdminTableLoad adminTableLoad) {
        return adminTableLoadRepository.save(adminTableLoad);
    }

    @Transactional
    public boolean deleteAdminTableLoadList(Integer reportId) {

        return adminTableLoadRepository.findById(reportId)
                .map(email -> {
                    adminTableLoadRepository.deleteById(reportId);
                    return true;
                })
                .orElse(false);
    }

    public Optional<AdminTableLoad> updateAdminTableLoadList(Integer reportId, AdminTableLoadDTO adminTableLoadDTO) {
        var adminTableListObj = adminTableLoadRepository.findById(reportId);
        if (adminTableListObj.isPresent() && adminTableLoadDTO != null) {
            AdminTableLoad adminTableLoad = adminTableListObj.get();
            adminTableLoad.setTableName(adminTableLoadDTO.getTableName());
            adminTableLoad.setReportId(adminTableLoadDTO.getReportId());
            adminTableLoad.setDbServer(adminTableLoadDTO.getDbServer());
            adminTableLoad.setDbIpAndPort(adminTableLoadDTO.getDbIpAndPort());
            adminTableLoad.setPassWord(adminTableLoadDTO.getPassWord());
            adminTableLoad.setUserId(adminTableLoadDTO.getUserId());
            adminTableLoad.setDatabase(adminTableLoadDTO.getDatabase());
            adminTableLoad.setDbDriver(adminTableLoadDTO.getDbDriver());
            return Optional.of(adminTableLoadRepository.save(adminTableLoad));
        }
        return Optional.empty();
    }

    public AdminTableLoadDTO getAdminTableLoadListbyReportId(Integer reportId) {

        Optional<AdminTableLoad> adminTableLoadList = adminTableLoadRepository.findById(reportId);
        if (adminTableLoadList.isPresent()) {
            AdminTableLoad adminTableLoadObj = adminTableLoadList.get();
            return new AdminTableLoadDTO(adminTableLoadObj);
        }else{
            return null;
        }
    }
}
