package admin.controller;
import admin.dto.AdminTableLoadDTO;
import admin.model.AdminTableLoad;
import admin.service.AdminTableLoadService;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/api/admin-table-load-list")
@CrossOrigin(origins = "http://localhost:3000")
public class AdminTableLoadController {

    private final AdminTableLoadService adminTableLoadService;

    // Constructor for dependency injection
    public AdminTableLoadController(AdminTableLoadService adminTableLoadService) {
        this.adminTableLoadService = adminTableLoadService;
    }

    @GetMapping("/all")
    public ResponseEntity<List<AdminTableLoad>> getAllAdminTableLoadList(){
        List<AdminTableLoad>  adminTableLoads = adminTableLoadService.getAllAdminTableLoadList();
        return ResponseEntity.ok(adminTableLoads);
    }


    @GetMapping("/{reportId}")
    public ResponseEntity<AdminTableLoadDTO> getAdminTableLoadReportId(@PathVariable Integer reportId) {

        if(reportId != null) {
            AdminTableLoadDTO adminTableLoadDTO = adminTableLoadService.getAdminTableLoadListbyReportId(reportId);
            return ResponseEntity.ok(adminTableLoadDTO);
        }
        else
        {
            return new ResponseEntity<>(HttpStatus.BAD_REQUEST);
        }
    }

    @PostMapping
    public ResponseEntity<AdminTableLoad> createAdminTableLoadList(@RequestBody AdminTableLoad adminTableLoad) {
        if(adminTableLoad != null) {
            AdminTableLoad adminTableLoadObj = adminTableLoadService.createAdminTableLoadList(adminTableLoad);
            return ResponseEntity.status(HttpStatus.CREATED).body(adminTableLoadObj);
        }
        else {
            return new ResponseEntity<>(HttpStatus.BAD_REQUEST);
        }
    }

    @DeleteMapping("/{reportId}")
    public ResponseEntity<String> deleteAdminTableLoadList(@PathVariable Integer reportId) {

        if(reportId != null) {
            boolean deleted = adminTableLoadService.deleteAdminTableLoadList(reportId);

            if (deleted) {
                return ResponseEntity.ok("Admin Table load List deleted successfully.");
            } else {
                return ResponseEntity.notFound().build();
            }
        }

        return new ResponseEntity<>(HttpStatus.BAD_REQUEST);
    }

    @PutMapping("/{reportId}")
    public ResponseEntity<AdminTableLoad> updateAdminTableLoadList(@PathVariable Integer reportId, @RequestBody AdminTableLoadDTO adminTableLoadDTO) {

        if(reportId != null && adminTableLoadDTO != null) {
            var currentAdminTableLoadObj = adminTableLoadService.updateAdminTableLoadList(reportId, adminTableLoadDTO);
            if(currentAdminTableLoadObj.isPresent()) {
                return ResponseEntity.ok(currentAdminTableLoadObj.get());
            }
        }
        return new ResponseEntity<>(HttpStatus.BAD_REQUEST);
    }
}
