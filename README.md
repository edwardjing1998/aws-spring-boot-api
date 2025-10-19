// src/main/java/rapid/service/sysprin/SysPrinBulkService.java
package rapid.service.sysprin;

import jakarta.persistence.EntityNotFoundException;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;
import rapid.model.sysprin.SysPrin;
import rapid.model.sysprin.key.SysPrinId;
import rapid.repository.sysprin.InvalidDelivAreaRepository;
import rapid.repository.sysprin.SysPrinRepository;
import rapid.web.sysprin.dto.ChangeAllResponse;

import java.util.*;
import java.util.stream.Collectors;

@Service
@RequiredArgsConstructor
public class SysPrinBulkService {

  private final SysPrinRepository sysPrinRepository;
  private final InvalidDelivAreaRepository invalidDelivAreaRepository;

  /**
   * Make every sysprin for this client match the template's settings,
   * and copy invalid_deliv_areas from template to others (no duplicates).
   */
  @Transactional
  public ChangeAllResponse copyAllFromTemplate(String clientId, String templateSysPrin) {
    // 0) Load template
    SysPrin template = sysPrinRepository.findById(new SysPrinId(clientId, templateSysPrin))
        .orElseThrow(() -> new EntityNotFoundException(
            "Template not found: client=" + clientId + ", sysPrin=" + templateSysPrin));

    // 1) Load all sysprins for this client (excluding template itself)
    List<SysPrin> others = sysPrinRepository.findAllByIdClient(clientId).stream()
        .filter(sp -> !Objects.equals(sp.getId().getSysPrin(), templateSysPrin))
        .toList();

    // 2) Overwrite non-key settings on each other sysprin
    int updated = 0;
    for (SysPrin sp : others) {
      if (copySettings(sp, template)) {
        updated++;
      }
    }
    if (updated > 0) {
      sysPrinRepository.saveAll(others);
    }

    // 3) Copy invalid_deliv_areas from template to others, deduped
    Set<String> templateAreas = invalidDelivAreaRepository.findAreasBySysPrin(templateSysPrin);
    int areasInserted = 0;

    if (!templateAreas.isEmpty()) {
      for (SysPrin sp : others) {
        String targetSysPrin = sp.getId().getSysPrin();

        // current areas for target
        Set<String> currentAreas = invalidDelivAreaRepository.findAreasBySysPrin(targetSysPrin);

        // compute missing ones
        List<String> toInsert = templateAreas.stream()
            .filter(a -> !currentAreas.contains(a))
            .collect(Collectors.toList());

        if (!toInsert.isEmpty()) {
          areasInserted += invalidDelivAreaRepository.bulkInsertAreas(targetSysPrin, toInsert);
        }
      }
    }

    return new ChangeAllResponse(clientId, templateSysPrin, updated, areasInserted);
  }

  // ---------- settings copier (explicit and safe) ----------

  private static <T> boolean setIfChanged(T current, T incoming, java.util.function.Consumer<T> setter) {
    if (!Objects.equals(current, incoming)) {
      setter.accept(incoming);
      return true;
    }
    return false;
  }

  /**
   * Copy non-key, configurable columns from template to target.
   * NOTE: Do not touch ID (client, sysPrin) or audit/version fields.
   */
  private boolean copySettings(SysPrin target, SysPrin template) {
    boolean changed = false;

    changed |= setIfChanged(target.getCustType(),           template.getCustType(),           target::setCustType);
    changed |= setIfChanged(target.getUndeliverable(),      template.getUndeliverable(),      target::setUndeliverable);
    changed |= setIfChanged(target.getStatA(),              template.getStatA(),              target::setStatA);
    changed |= setIfChanged(target.getStatB(),              template.getStatB(),              target::setStatB);
    changed |= setIfChanged(target.getStatC(),              template.getStatC(),              target::setStatC);
    changed |= setIfChanged(target.getStatD(),              template.getStatD(),              target::setStatD);
    changed |= setIfChanged(target.getStatE(),              template.getStatE(),              target::setStatE);
    changed |= setIfChanged(target.getStatF(),              template.getStatF(),              target::setStatF);
    changed |= setIfChanged(target.getStatI(),              template.getStatI(),              target::setStatI);
    changed |= setIfChanged(target.getStatL(),              template.getStatL(),              target::setStatL);
    changed |= setIfChanged(target.getStatO(),              template.getStatO(),              target::setStatO);
    changed |= setIfChanged(target.getStatU(),              template.getStatU(),              target::setStatU);
    changed |= setIfChanged(target.getStatX(),              template.getStatX(),              target::setStatX);
    changed |= setIfChanged(target.getStatZ(),              template.getStatZ(),              target::setStatZ);

    changed |= setIfChanged(target.getPoBox(),              template.getPoBox(),              target::setPoBox);
    changed |= setIfChanged(target.getAddrFlag(),           template.getAddrFlag(),           target::setAddrFlag);
    changed |= setIfChanged(target.getTempAway(),           template.getTempAway(),           target::setTempAway);
    changed |= setIfChanged(target.getRps(),                template.getRps(),                target::setRps);
    changed |= setIfChanged(target.getSession(),            template.getSession(),            target::setSession);
    changed |= setIfChanged(target.getBadState(),           template.getBadState(),           target::setBadState);
    changed |= setIfChanged(target.getAstatRch(),           template.getAstatRch(),           target::setAstatRch);
    changed |= setIfChanged(target.getNm13(),               template.getNm13(),               target::setNm13);
    changed |= setIfChanged(target.getTempAwayAtts(),       template.getTempAwayAtts(),       target::setTempAwayAtts);
    changed |= setIfChanged(target.getReportMethod(),       template.getReportMethod(),       target::setReportMethod);
    changed |= setIfChanged(target.getActive(),             template.getActive(),             target::setActive);
    changed |= setIfChanged(target.getNotes(),              template.getNotes(),              target::setNotes);
    changed |= setIfChanged(target.getReturnStatus(),       template.getReturnStatus(),       target::setReturnStatus);
    changed |= setIfChanged(target.getDestroyStatus(),      template.getDestroyStatus(),      target::setDestroyStatus);
    changed |= setIfChanged(target.getNonUS(),              template.getNonUS(),              target::setNonUS);
    changed |= setIfChanged(target.getSpecial(),            template.getSpecial(),            target::setSpecial);
    changed |= setIfChanged(target.getPinMailer(),          template.getPinMailer(),          target::setPinMailer);
    changed |= setIfChanged(target.getForwardingAddress(),  template.getForwardingAddress(),  target::setForwardingAddress);
    changed |= setIfChanged(target.getHoldDays(),           template.getHoldDays(),           target::setHoldDays);
    changed |= setIfChanged(target.getContact(),            template.getContact(),            target::setContact);
    changed |= setIfChanged(target.getPhone(),              template.getPhone(),              target::setPhone);
    changed |= setIfChanged(target.getEntityCode(),         template.getEntityCode(),         target::setEntityCode);

    return changed;
  }
}
