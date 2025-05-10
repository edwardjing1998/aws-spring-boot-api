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
