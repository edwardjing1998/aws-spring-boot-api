package rapid.web.sysprin;

import lombok.RequiredArgsConstructor;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;
import rapid.service.sysprin.SysPrinBulkService;
import rapid.web.sysprin.dto.ChangeAllResponse;

@RestController
@RequestMapping("/api")
@RequiredArgsConstructor
public class SysPrinBulkController {

  private final SysPrinBulkService sysPrinBulkService;

  /**
   * Copy the settings of {templateSysPrin} to all other sysprins under {clientId},
   * and copy invalid_deliv_areas (deduped).
   */
  @PostMapping("/clients/{clientId}/sysprins/copy-from/{templateSysPrin}")
  public ResponseEntity<ChangeAllResponse> copyAllFromTemplate(
      @PathVariable String clientId,
      @PathVariable String templateSysPrin) {

    ChangeAllResponse result =
        sysPrinBulkService.copyAllFromTemplate(clientId, templateSysPrin);

    return ResponseEntity.ok(result);
  }
}
