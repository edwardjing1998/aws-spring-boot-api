package admin.controller;


import admin.dto.AdminDataDefinitionsDTO;
import admin.dto.AdminQueryListDTO;
import admin.model.AdminDataDefinitionsList;
import admin.model.AdminQueryList;
import admin.service.AdminDataDefinitonsService;
import admin.service.AdminQueryListService;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/api/admin-data-definitions")
@CrossOrigin(origins = "http://localhost:3000")
public class AdminDataDefinitionsController {

    private final AdminDataDefinitonsService adminDataDefinitonsService;

    // Constructor for dependency injection
    public AdminDataDefinitionsController(AdminDataDefinitonsService adminDataDefinitonsService) {
        this.adminDataDefinitonsService = adminDataDefinitonsService;
    }


    @GetMapping("/{reportId}")
    public ResponseEntity<AdminDataDefinitionsDTO> getAdminDataDefinitionListbyReportId(@PathVariable Integer reportId) {

        if(reportId != null) {
            AdminDataDefinitionsDTO adminDataDefinitionsDTO = adminDataDefinitonsService.getAdminDataDefinitionsListbyReportId(reportId);
            return ResponseEntity.ok(adminDataDefinitionsDTO);
        }
        else
        {
            return new ResponseEntity<>(HttpStatus.BAD_REQUEST);
        }
    }

    @PostMapping
    public ResponseEntity<AdminDataDefinitionsList> createAdminDataDefinitionList(@RequestBody AdminDataDefinitionsList  adminDataDefinitionsList) {
        if(adminDataDefinitionsList != null) {
            AdminDataDefinitionsList adminDataDefinitionsListObj = adminDataDefinitonsService.createAdminDataDefinitionList(adminDataDefinitionsList);
            return ResponseEntity.status(HttpStatus.CREATED).body(adminDataDefinitionsListObj);
        }
        else {
            return new ResponseEntity<>(HttpStatus.BAD_REQUEST);
        }
    }

    @DeleteMapping("/{reportId}")
    public ResponseEntity<String> deleteAdminDataDefinitionList(@PathVariable Integer reportId) {

        if(reportId != null) {
            boolean deleted = adminDataDefinitonsService.deleteAdminDataDefinitionList(reportId);

            if (deleted) {
                return ResponseEntity.ok("Admin Data Definition List deleted successfully.");
            } else {
                return ResponseEntity.notFound().build();
            }
        }

        return new ResponseEntity<>(HttpStatus.BAD_REQUEST);
    }

    @PutMapping("/{reportId}")
    public ResponseEntity<AdminDataDefinitionsList> updateAdminDataDefinitionList(@PathVariable Integer reportId, @RequestBody AdminDataDefinitionsDTO adminDataDefinitionsDTO) {

        if(reportId != null && adminDataDefinitionsDTO != null) {
            var currentAdminDataDefinitionListObj = adminDataDefinitonsService.updateAdminDataDefinitionList(reportId, adminDataDefinitionsDTO);
            if(currentAdminDataDefinitionListObj.isPresent()) {
                return ResponseEntity.ok(currentAdminDataDefinitionListObj.get());
            }
        }
        return new ResponseEntity<>(HttpStatus.BAD_REQUEST);
    }
}
