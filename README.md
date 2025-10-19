package rapid.service.sysprin;

import jakarta.persistence.EntityNotFoundException;
import jakarta.persistence.EntityManager;
import jakarta.persistence.PersistenceContext;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;
import rapid.repository.sysprin.SysPrinNativeRepository;
import rapid.repository.sysprin.InvalidDelivAreaNativeRepository;
import rapid.web.sysprin.dto.ChangeAllResponse;

import java.util.List;
import java.util.Objects;

@Service
@RequiredArgsConstructor
public class SysPrinBulkService {

  private final SysPrinNativeRepository sysPrinNative;
  private final InvalidDelivAreaNativeRepository invalidNative;

  @PersistenceContext
  private EntityManager em;

  @Transactional
  public ChangeAllResponse copyAllFromTemplate(String clientId, String templateSysPrin) {
    // Ensure the persistence context is clean (no managed SysPrin entities to collide)
    em.clear();

    var template = sysPrinNative.findTemplateRow(clientId, templateSysPrin)
        .orElseThrow(() -> new EntityNotFoundException(
            "Template not found: client=" + clientId + ", sysPrin=" + templateSysPrin));

    // Distinct sys_prin keys for client
    List<String> keys = sysPrinNative.findDistinctSysPrinsByClient(clientId);
    List<String> targets = keys.stream()
        .filter(k -> !Objects.equals(k, templateSysPrin))
        .toList();

    // Bulk update each key via native UPDATE (updates ALL duplicate rows for that key)
    int rowsUpdated = 0;
    for (String sysPrin : targets) {
      rowsUpdated += sysPrinNative.updateSettingsForKey(
          clientId, sysPrin,
          template.getCUST_TYPE(), template.getUNDELIVERABLE(),
          template.getSTAT_A(), template.getSTAT_B(), template.getSTAT_C(), template.getSTAT_D(),
          template.getSTAT_E(), template.getSTAT_F(), template.getSTAT_I(), template.getSTAT_L(),
          template.getSTAT_O(), template.getSTAT_U(), template.getSTAT_X(), template.getSTAT_Z(),
          template.getPO_BOX(), template.getADDR_FLAG(), template.getTEMP_AWAY(),
          template.getRPS(), template.getSESSION(), template.getBAD_STATE(),
          template.getA_STAT_RCH(), template.getNM_13(), template.getTEMP_AWAY_ATTS(),
          template.getREPORT_METHOD(), template.getACTIVE(), template.getNOTES(),
          template.getRET_STAT(), template.getDES_STAT(), template.getNON_US(), template.getSPECIAL(),
          template.getPIN(), template.getFORWARDING_ADDR(), template.getHOLD_DAYS(),
          template.getCONTACT(), template.getPHONE(), template.getENTITY_CD()
      );
    }

    // Copy invalid_deliv_areas via native INSERT .. NOT EXISTS (deduped)
    int areasInserted = 0;
    for (String target : targets) {
      areasInserted += invalidNative.copyAreasFromTemplate(templateSysPrin, target);
    }

    // rowsUpdated is count of physical rows touched (duplicates included).
    // If you prefer "how many sys_prins changed", use targets.size().
    return new ChangeAllResponse(clientId, templateSysPrin, targets.size(), areasInserted);
  }
}
