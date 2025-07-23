package admin.controller;

import admin.dto.AdminQueryToolTipsDTO;
import admin.model.AdminQueryToolTips;
import admin.service.AdminQueryToolTipsService;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/api/admin-query-tool-tips-mapping")
@CrossOrigin(origins = "http://localhost:3000")
public class AdminQueryToolTipsController {

    private final AdminQueryToolTipsService adminQueryToolTipsService;

    // Constructor for dependency injection
    public AdminQueryToolTipsController(AdminQueryToolTipsService adminQueryToolTipsService) {
        this.adminQueryToolTipsService = adminQueryToolTipsService;
    }

    @GetMapping("/all")
    public ResponseEntity<List<AdminQueryToolTips>> getAllQueryToolTipsList(){
        List<AdminQueryToolTips>  adminQueryToolTipsList = adminQueryToolTipsService.getallTooltipsList();
        return ResponseEntity.ok(adminQueryToolTipsList);
    }


    @GetMapping("/{reportId}")
    public ResponseEntity<AdminQueryToolTipsDTO> getAllQueryTooltipReportId(@PathVariable Integer reportId) {

        if(reportId != null) {
            AdminQueryToolTipsDTO adminQueryToolTipsDTO = adminQueryToolTipsService.getallTooltipsListbyReportId(reportId);
            return ResponseEntity.ok(adminQueryToolTipsDTO);
        }
        else
        {
            return new ResponseEntity<>(HttpStatus.BAD_REQUEST);
        }
    }

    @PostMapping
    public ResponseEntity<AdminQueryToolTips> createAdminQueryToolTipList(@RequestBody AdminQueryToolTips adminQueryToolTips) {
        if(adminQueryToolTips != null) {
            AdminQueryToolTips adminQueryToolTipsObj = adminQueryToolTipsService.createAdminQueryToolTipList(adminQueryToolTips);
            return ResponseEntity.status(HttpStatus.CREATED).body(adminQueryToolTipsObj);
        }
        else {
            return new ResponseEntity<>(HttpStatus.BAD_REQUEST);
        }
    }

    @DeleteMapping("/{reportId}")
    public ResponseEntity<String> deleteAdminQueryToolTipList(@PathVariable Integer reportId) {

        if(reportId != null) {
            boolean deleted = adminQueryToolTipsService.deleteAdminQueryToolTipList(reportId);

            if (deleted) {
                return ResponseEntity.ok("Admin Table Query Tool Tip List deleted successfully.");
            } else {
                return ResponseEntity.notFound().build();
            }
        }

        return new ResponseEntity<>(HttpStatus.BAD_REQUEST);
    }

    @PutMapping
    public ResponseEntity<AdminQueryToolTips> updateAdminTableLoadList(@RequestBody AdminQueryToolTipsDTO adminQueryToolTipsDTO) {

        if(adminQueryToolTipsDTO != null) {
            var currentAdminQueryTooltipObj = adminQueryToolTipsService.updateAdminQueryToolTipsList(adminQueryToolTipsDTO);
            if(currentAdminQueryTooltipObj.isPresent()) {
                return ResponseEntity.ok(currentAdminQueryTooltipObj.get());
            }
        }
        return new ResponseEntity<>(HttpStatus.BAD_REQUEST);
    }
}
