package admin.service;


import admin.dto.AdminQueryListDTO;
import admin.model.AdminQueryList;
import admin.repository.AdminQueryListRepository;
import jakarta.persistence.criteria.CriteriaBuilder;
import jakarta.transaction.Transactional;
import org.springframework.stereotype.Service;

import java.util.List;
import java.util.Optional;

@Service
public class AdminQueryListService {

    private final AdminQueryListRepository adminQueryListRepository;

    public AdminQueryListService(AdminQueryListRepository  adminQueryListRepository) {
        this.adminQueryListRepository = adminQueryListRepository;
    }

    public List<AdminQueryList> getAllAdminQueryList() {
        return adminQueryListRepository.findAll();
    }

    public AdminQueryListDTO getAdminQueryListbyReportId(Integer reportId) {
        Optional<AdminQueryList> adminQueryList = adminQueryListRepository.findById(reportId);
        if (adminQueryList.isPresent()) {
            AdminQueryList adminQueryListObj = adminQueryList.get();
            return new AdminQueryListDTO(adminQueryListObj);
        }else{
            return null;
        }
    }

    public List<AdminQueryList> getAdminQueryListbyFileTransferType(Integer fileTransferType) {
        var adminQueryList = adminQueryListRepository.findAllByFileTransferType(fileTransferType);
        if (adminQueryList.size() > 0) {
            return adminQueryList;
        }else{
            return null;
        }
    }

    public AdminQueryList createAdminQueryList(AdminQueryList adminQueryList) {
        return adminQueryListRepository.save(adminQueryList);
    }

    @Transactional
    public boolean deleteAdminQueryList(Integer reportId) {

        return adminQueryListRepository.findById(reportId)
                .map(email -> {
                    adminQueryListRepository.deleteById(reportId);
                    return true;
                })
                .orElse(false);
    }

    public Optional<AdminQueryList> updateAdminQueryList(Integer reportId, AdminQueryListDTO adminQueryListDTO) {
        var adminQueryListObj = adminQueryListRepository.findById(reportId);
        if (adminQueryListObj.isPresent() && adminQueryListDTO != null) {
            AdminQueryList adminQueryList = adminQueryListObj.get();
            adminQueryList.setQueryName(adminQueryListDTO.getQueryName());
            adminQueryList.setQuery(adminQueryListDTO.getQuery());
            adminQueryList.setInputDataFields(adminQueryListDTO.getInputDataFields());
            adminQueryList.setFileExt(adminQueryListDTO.getFileExt());
            adminQueryList.setDbDriverType(adminQueryListDTO.getDbDriverType());
            adminQueryList.setFileHeaderInd(adminQueryListDTO.getFileHeaderInd());
            adminQueryList.setDefaultFileNm(adminQueryListDTO.getDefaultFileNm());
            adminQueryList.setReportDbServer(adminQueryListDTO.getReportDbServer());
            adminQueryList.setReportDb(adminQueryListDTO.getReportDb());
            adminQueryList.setReportDbUserid(adminQueryListDTO.getReportDbUserid());
            adminQueryList.setReportDbPasswrd(adminQueryListDTO.getReportDbPasswrd());
            adminQueryList.setFileTransferType(adminQueryListDTO.getFileTransferType());
            adminQueryList.setReportDbIpAndPort(adminQueryListDTO.getReportDbIpAndPort());
            adminQueryList.setReportByClientFlag(adminQueryListDTO.getReportByClientFlag());
            adminQueryList.setRerunDateRangeStart(adminQueryListDTO.getRerunDateRangeStart());
            adminQueryList.setRerunDateRangeEnd(adminQueryListDTO.getRerunDateRangeEnd());
            adminQueryList.setRerunClientId(adminQueryListDTO.getRerunClientId());
            adminQueryList.setEmailFromAddress(adminQueryListDTO.getEmailFromAddress());
            adminQueryList.setEmailEventId(adminQueryListDTO.getEmailEventId());
            adminQueryList.setTabDelimitedFlag(adminQueryListDTO.getTabDelimitedFlag());
            adminQueryList.setInputFileTx(adminQueryListDTO.getInputFileTx());
            adminQueryList.setInputFileKeyStartPos(adminQueryListDTO.getInputFileKeyStartPos());
            adminQueryList.setInputFileKeyLength(adminQueryListDTO.getInputFileKeyLength());
            adminQueryList.setAccessLevel(adminQueryListDTO.getAccessLevel());
            adminQueryList.setIsActive(adminQueryListDTO.getIsActive());
            adminQueryList.setIsVisible(adminQueryListDTO.getIsVisible());
            adminQueryList.setNumSheets(adminQueryListDTO.getNumSheets());
            return Optional.of(adminQueryListRepository.save(adminQueryList));
        }
        return Optional.empty();
    }
}
