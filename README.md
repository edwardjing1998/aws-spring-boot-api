package admin.service;

import admin.dto.C3FileTransferDTO;
import admin.model.C3FileTransfer;
import admin.model.AdminQueryList;
import admin.repository.AdminQueryListRepository;
import admin.repository.C3FileTransferRepository;
import jakarta.transaction.Transactional;
import org.springframework.stereotype.Service;

import java.util.List;
import java.util.Optional;
import java.util.stream.Collectors;

@Service
public class C3FileTransferService {

    private final C3FileTransferRepository c3FileTransferRepository;
    private final AdminQueryListRepository adminQueryListRepository;


    public C3FileTransferService(C3FileTransferRepository c3FileTransferRepository, AdminQueryListRepository adminQueryListRepository) {
        this.c3FileTransferRepository = c3FileTransferRepository;
        this.adminQueryListRepository = adminQueryListRepository;
    }


    @Transactional
    public boolean deleteC3fileTransferList(Integer reportId) {
        Optional<AdminQueryList> optionalAdminQuery = adminQueryListRepository.findById(reportId);

        if (optionalAdminQuery.isPresent()) {
            AdminQueryList adminQuery = optionalAdminQuery.get();

            // Step 1: delete C3FileTransfer manually
            C3FileTransfer relatedTransfer = adminQuery.getC3FileTransfer();
            if (relatedTransfer != null) {
                c3FileTransferRepository.delete(relatedTransfer);
            }

            // Step 2: delete AdminQueryList
            adminQueryListRepository.delete(adminQuery);

            return true;
        }

        return false;
    }


    public List<C3FileTransferDTO> getAllTransfers() {

        List<C3FileTransfer> entities = c3FileTransferRepository.findAll();

        return entities.stream().map(entity -> {
            C3FileTransferDTO dto = new C3FileTransferDTO();
            dto.setFileTransId(entity.getFileTransId());
            dto.setSequenceNr(entity.getSequenceNr());
            dto.setTransferCd(entity.getTransferCd());
            dto.setProtocolNm(entity.getProtocolNm());
            dto.setTransPrgNm(entity.getTransPrgNm());
            dto.setModeNm(entity.getModeNm());
            dto.setSecurityNm(entity.getSecurityNm());
            dto.setIpPortCd(entity.getIpPortCd());
            dto.setListenerSrvNm(entity.getListenerSrvNm());
            dto.setDdm(entity.getDdNm());
            dto.setOrgTypeCd(entity.getOrgTypeCd());
            dto.setMemberCd(entity.getMemberCd());
            dto.setRecordLengthNr(entity.getRecordLengthNr());
            dto.setBlockSizeNr(entity.getBlockSizeNr());
            dto.setConvertFileCd(entity.getConvertFileCd());
            dto.setXferFileNm(entity.getXferFileNm());
            dto.setJobNm(entity.getJobNm());
            dto.setProgramNm(entity.getProgramNm());
            dto.setRemoteFileNm(entity.getRemoteFileNm());
            dto.setLocalFileNm(entity.getLocalFileNm());
            dto.setControlFileNm(entity.getControlFileNm());
            dto.setGatewayAccessCd(entity.getGatewayAccessCd());
            dto.setBinFileCRLFInf(entity.getBinFileCRLFind());

            AdminQueryList adminQueryList = entity.getAdminQueryList();
            if (adminQueryList != null) {
                dto.setAdminQueryList(adminQueryList);
            }
            return dto;
        }).collect(Collectors.toList());
    }

      public C3FileTransfer createC3fileTransfer(C3FileTransfer c3FileTransfer) {
        return c3FileTransferRepository.save(c3FileTransfer);
    }

    public Optional<C3FileTransfer> updateC3fileTransferList(Integer transId, C3FileTransferDTO c3FileTransferDTO) {

        var c3FileTransferObj = c3FileTransferRepository.findById(transId);
        if (c3FileTransferObj.isPresent() && c3FileTransferDTO != null) {
            var c3FileTransfer = c3FileTransferObj.get();
            c3FileTransfer.setFileTransId(c3FileTransferDTO.getFileTransId());
            c3FileTransfer.setSequenceNr(c3FileTransferDTO.getSequenceNr());
            c3FileTransfer.setTransferCd(c3FileTransferDTO.getTransferCd());
            c3FileTransfer.setProtocolNm(c3FileTransferDTO.getProtocolNm());
            c3FileTransfer.setTransPrgNm(c3FileTransferDTO.getTransPrgNm());
            c3FileTransfer.setModeNm(c3FileTransferDTO.getModeNm());
            c3FileTransfer.setSecurityNm(c3FileTransferDTO.getSecurityNm());
            c3FileTransfer.setIpPortCd(c3FileTransferDTO.getIpPortCd());
            c3FileTransfer.setListenerSrvNm(c3FileTransferDTO.getListenerSrvNm());
            c3FileTransfer.setDdNm(c3FileTransferDTO.getDdNm());
            c3FileTransfer.setOrgTypeCd(c3FileTransferDTO.getOrgTypeCd());
            c3FileTransfer.setMemberCd(c3FileTransferDTO.getMemberCd());
            c3FileTransfer.setRecordLengthNr(c3FileTransferDTO.getRecordLengthNr());
            c3FileTransfer.setBlockSizeNr(c3FileTransferDTO.getBlockSizeNr());
            c3FileTransfer.setConvertFileCd(c3FileTransferDTO.getConvertFileCd());
            c3FileTransfer.setXferFileNm(c3FileTransferDTO.getXferFileNm());
            c3FileTransfer.setJobNm(c3FileTransferDTO.getJobNm());
            c3FileTransfer.setProgramNm(c3FileTransferDTO.getProgramNm());
            c3FileTransfer.setLocalFileNm(c3FileTransferDTO.getLocalFileNm());
            c3FileTransfer.setControlFileNm(c3FileTransferDTO.getControlFileNm());
            c3FileTransfer.setGatewayAccessCd(c3FileTransferDTO.getGatewayAccessCd());
            c3FileTransfer.setBinFileCRLFind(c3FileTransferDTO.getBinFileCRLFInf());
            return Optional.of(c3FileTransferRepository.save(c3FileTransfer));
        }

        return Optional.empty();
    }
}
