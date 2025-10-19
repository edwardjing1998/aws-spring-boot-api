// src/main/java/rapid/web/sysprin/SysPrinDuplicateController.java
package rapid.web.sysprin;

import jakarta.persistence.EntityNotFoundException;
import lombok.RequiredArgsConstructor;
import org.springframework.http.*;
import org.springframework.web.bind.annotation.*;
import rapid.service.sysprin.SysPrinDuplicateService;

@RestController
@RequestMapping("/client-sysprin-writer/api")
@RequiredArgsConstructor
public class SysPrinDuplicateController {

  private final SysPrinDuplicateService service;

  /**
   * Duplicate a sys_prin to a new sys_prin for the same client.
   *
   * Example:
   * POST /client-sysprin-writer/api/clients/0016/sysprins/57491000/duplicate-to/57492000?overwrite=false&copyAreas=true
   */
  @PostMapping("/clients/{clientId}/sysprins/{sourceSysPrin}/duplicate-to/{targetSysPrin}")
  public ResponseEntity<SysPrinDuplicateService.DuplicateResult> duplicate(
      @PathVariable String clientId,
      @PathVariable String sourceSysPrin,
      @PathVariable String targetSysPrin,
      @RequestParam(defaultValue = "false") boolean overwrite,
      @RequestParam(defaultValue = "true") boolean copyAreas
  ) {
    var result = service.duplicate(clientId, sourceSysPrin, targetSysPrin, overwrite, copyAreas);
    return ResponseEntity.status(HttpStatus.OK).body(result);
  }

  @ExceptionHandler(EntityNotFoundException.class)
  public ResponseEntity<String> notFound(EntityNotFoundException ex) {
    return ResponseEntity.status(HttpStatus.NOT_FOUND).body(ex.getMessage());
  }

  @ExceptionHandler(IllegalStateException.class)
  public ResponseEntity<String> conflict(IllegalStateException ex) {
    return ResponseEntity.status(HttpStatus.CONFLICT).body(ex.getMessage());
  }
}
