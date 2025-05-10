package admin.service;


import admin.dto.AdminDataDefinitionsDTO;
import admin.dto.AdminQueryListDTO;
import admin.model.AdminDataDefinitionsList;
import admin.model.AdminQueryList;
import admin.repository.AdminDataDefinitionsRepository;
import admin.repository.AdminQueryListRepository;
import jakarta.transaction.Transactional;
import org.springframework.stereotype.Service;

import java.util.List;
import java.util.Optional;

@Service
public class AdminDataDefinitonsService {

    private final AdminDataDefinitionsRepository adminDataDefinitonsRepository;

    public AdminDataDefinitonsService(AdminDataDefinitionsRepository  adminDataDefinitonsRepository) {
        this.adminDataDefinitonsRepository = adminDataDefinitonsRepository;
    }

    public AdminDataDefinitionsDTO getAdminDataDefinitionsListbyReportId(Integer reportId) {
        Optional<AdminDataDefinitionsList> adminDataDefinitionsList = adminDataDefinitonsRepository.findById(reportId);
        if (adminDataDefinitionsList.isPresent()) {
            AdminDataDefinitionsList dataDefinitionsList = adminDataDefinitionsList.get();
            return new AdminDataDefinitionsDTO(dataDefinitionsList);
        }else{
            return null;
        }
    }

    public AdminDataDefinitionsList createAdminDataDefinitionList(AdminDataDefinitionsList adminDataDefinitionsList) {
        return adminDataDefinitonsRepository.save(adminDataDefinitionsList);
    }

    @Transactional
    public boolean deleteAdminDataDefinitionList(Integer reportId) {

        return adminDataDefinitonsRepository.findById(reportId)
                .map(email -> {
                    adminDataDefinitonsRepository.deleteById(reportId);
                    return true;
                })
                .orElse(false);
    }

    public Optional<AdminDataDefinitionsList> updateAdminDataDefinitionList(Integer reportId, AdminDataDefinitionsDTO adminDataDefinitionsDTO) {
        var adminDataDefinitionListObj = adminDataDefinitonsRepository.findById(reportId);
        if (adminDataDefinitionListObj.isPresent() && adminDataDefinitionsDTO != null) {
            AdminDataDefinitionsList adminDataDefinitionsList = adminDataDefinitionListObj.get();
            adminDataDefinitionsList.setInput0DataLength(adminDataDefinitionsDTO.getInput0DataLength());
            adminDataDefinitionsList.setInput1DataLength(adminDataDefinitionsDTO.getInput1DataLength());
            adminDataDefinitionsList.setInput2DataLength(adminDataDefinitionsDTO.getInput2DataLength());
            adminDataDefinitionsList.setInput3DataLength(adminDataDefinitionsDTO.getInput3DataLength());
            adminDataDefinitionsList.setInput4DataLength(adminDataDefinitionsDTO.getInput4DataLength());
            adminDataDefinitionsList.setInput5DataLength(adminDataDefinitionsDTO.getInput5DataLength());
            adminDataDefinitionsList.setInput6DataLength(adminDataDefinitionsDTO.getInput6DataLength());
            adminDataDefinitionsList.setInput7DataLength(adminDataDefinitionsDTO.getInput7DataLength());
            adminDataDefinitionsList.setInput8DataLength(adminDataDefinitionsDTO.getInput8DataLength());
            adminDataDefinitionsList.setInput0DataMask(adminDataDefinitionsDTO.getInput0DataMask());
            adminDataDefinitionsList.setInput1DataMask(adminDataDefinitionsDTO.getInput1DataMask());
            adminDataDefinitionsList.setInput2DataMask(adminDataDefinitionsDTO.getInput2DataMask());
            adminDataDefinitionsList.setInput3DataMask(adminDataDefinitionsDTO.getInput3DataMask());
            adminDataDefinitionsList.setInput4DataMask(adminDataDefinitionsDTO.getInput4DataMask());
            adminDataDefinitionsList.setInput5DataMask(adminDataDefinitionsDTO.getInput5DataMask());
            adminDataDefinitionsList.setInput6DataMask(adminDataDefinitionsDTO.getInput6DataMask());
            adminDataDefinitionsList.setInput7DataMask(adminDataDefinitionsDTO.getInput7DataMask());
            adminDataDefinitionsList.setInput8DataMask(adminDataDefinitionsDTO.getInput8DataMask());
            adminDataDefinitionsList.setInput0DataType(adminDataDefinitionsDTO.getInput0DataType());
            adminDataDefinitionsList.setInput1DataType(adminDataDefinitionsDTO.getInput1DataType());
            adminDataDefinitionsList.setInput2DataType(adminDataDefinitionsDTO.getInput2DataType());
            adminDataDefinitionsList.setInput3DataType(adminDataDefinitionsDTO.getInput3DataType());
            adminDataDefinitionsList.setInput4DataType(adminDataDefinitionsDTO.getInput4DataType());
            adminDataDefinitionsList.setInput5DataType(adminDataDefinitionsDTO.getInput5DataType());
            adminDataDefinitionsList.setInput6DataType(adminDataDefinitionsDTO.getInput6DataType());
            adminDataDefinitionsList.setInput7DataType(adminDataDefinitionsDTO.getInput7DataType());
            adminDataDefinitionsList.setInput8DataType(adminDataDefinitionsDTO.getInput8DataType());
            return Optional.of(adminDataDefinitonsRepository.save(adminDataDefinitionsList));
        }
        return Optional.empty();
    }
}
