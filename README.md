package admin.controller;

import admin.dto.C3FileTransferDTO;
import admin.model.C3FileTransfer;
import admin.service.C3FileTransferService;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/api/c3-filetransfer")
@CrossOrigin(origins = "http://localhost:3000")
public class C3FileTransferController {

    private final C3FileTransferService c3FileTransferService;

    // Constructor for dependency injection
    public C3FileTransferController(C3FileTransferService c3FileTransferService) {
        this.c3FileTransferService = c3FileTransferService;
    }

    @GetMapping
    public List<C3FileTransferDTO> getAllTransfers() {
        return c3FileTransferService.getAllTransfers();
    }

    @PostMapping
    public ResponseEntity<C3FileTransfer> createC3filetransferist(@RequestBody C3FileTransfer c3FileTransfer) {
        if(c3FileTransfer != null) {
            C3FileTransfer c3FileTransferObj = c3FileTransferService.createC3fileTransfer(c3FileTransfer);
            return ResponseEntity.status(HttpStatus.CREATED).body(c3FileTransferObj);
        }
        else {
            return new ResponseEntity<>(HttpStatus.BAD_REQUEST);
        }
    }

    @DeleteMapping("/{filetrnsId}")
    public ResponseEntity<String> deleteC3fileTransferList(@PathVariable Integer filetrnsId) {

        if(filetrnsId != null) {
            boolean deleted = c3FileTransferService.deleteC3fileTransferList(filetrnsId);

            if (deleted) {
                return ResponseEntity.ok("C3 file transfer List deleted successfully.");
            } else {
                return ResponseEntity.notFound().build();
            }
        }

        return new ResponseEntity<>(HttpStatus.BAD_REQUEST);
    }

    @PutMapping("/{filetrnsId}")
    public ResponseEntity<C3FileTransfer> updateC3filetransferList(@PathVariable Integer filetransId, @RequestBody C3FileTransferDTO c3FileTransferDTO) {

        if (filetransId != null && c3FileTransferDTO != null) {
            var currentC3filetransferListObj = c3FileTransferService.updateC3fileTransferList(filetransId, c3FileTransferDTO);
            if (currentC3filetransferListObj.isPresent()) {
                return ResponseEntity.ok(currentC3filetransferListObj.get());
            }
        }
        return new ResponseEntity<>(HttpStatus.BAD_REQUEST);
    }

}







package admin.service;

import admin.dto.C3FileTransferDTO;
import admin.model.C3FileTransfer;
import admin.model.AdminQueryList;
import admin.repository.C3FileTransferRepository;
import jakarta.transaction.Transactional;
import org.springframework.stereotype.Service;

import java.util.List;
import java.util.Optional;
import java.util.stream.Collectors;

@Service
public class C3FileTransferService {

    private final C3FileTransferRepository c3FileTransferRepository;

    public C3FileTransferService(C3FileTransferRepository c3FileTransferRepository) {
        this.c3FileTransferRepository = c3FileTransferRepository;
    }

    @Transactional
    public boolean deleteC3fileTransferList(Integer fileTrnsId) {

        return c3FileTransferRepository.findById(fileTrnsId)
                .map(email -> {
                    c3FileTransferRepository.deleteById(fileTrnsId);
                    return true;
                })
                .orElse(false);
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

