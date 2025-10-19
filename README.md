// src/main/java/rapid/web/sysprin/SysPrinBulkController.java
package rapid.web.sysprin;

import jakarta.persistence.EntityNotFoundException;
import lombok.RequiredArgsConstructor;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;
import rapid.service.sysprin.SysPrinBulkService;
import rapid.web.sysprin.dto.ChangeAllResponse;

@RestController
@RequestMapping("/client-sysprin-writer/api")
@RequiredArgsConstructor
public class SysPrinBulkController {

  private final SysPrinBulkService sysPrinBulkService;

  /**
   * Copy the settings of {templateSysPrin} to all other sysprins under {clientId},
   * and copy invalid_deliv_areas (deduped). Uses native SQL and is tolerant to duplicate legacy rows.
   *
   * Example:
   * curl -X POST "http://localhost:8089/client-sysprin-writer/api/clients/0016/sysprins/copy-from/57491000"
   */
  @PostMapping("/clients/{clientId}/sysprins/copy-from/{templateSysPrin}")
  public ResponseEntity<ChangeAllResponse> copyAllFromTemplate(
      @PathVariable String clientId,
      @PathVariable String templateSysPrin
  ) {
    ChangeAllResponse result = sysPrinBulkService.copyAllFromTemplate(clientId, templateSysPrin);
    return ResponseEntity.ok(result);
  }

  // --- Simple error mapping for "template not found" ---
  @ExceptionHandler(EntityNotFoundException.class)
  public ResponseEntity<String> handleNotFound(EntityNotFoundException ex) {
    return ResponseEntity.status(HttpStatus.NOT_FOUND).body(ex.getMessage());
  }
}
