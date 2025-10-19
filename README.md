// src/main/java/rapid/service/sysprin/SysPrinDuplicateService.java
package rapid.service.sysprin;

import jakarta.persistence.EntityNotFoundException;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;
import rapid.repository.sysprin.SysPrinDuplicateNativeRepository;

@Service
@RequiredArgsConstructor
public class SysPrinDuplicateService {

  private final SysPrinDuplicateNativeRepository repo;

  public record DuplicateResult(
      String clientId, String sourceSysPrin, String targetSysPrin,
      boolean inserted, int rowsUpdated, int areasCopied
  ) {}

  /**
   * Duplicate one sys_prin to a new sys_prin (same client).
   * - If target exists and overwrite=false -> throws IllegalStateException.
   * - If target exists and overwrite=true  -> bulk UPDATE target to match source.
   * - Always copies invalid_deliv_areas when copyAreas=true.
   */
  @Transactional
  public DuplicateResult duplicate(
      String clientId, String sourceSysPrin, String targetSysPrin,
      boolean overwrite, boolean copyAreas
  ) {
    // Ensure source exists (at least one physical row)
    int srcCount = repo.countByKey(clientId, sourceSysPrin);
    if (srcCount <= 0) {
      throw new EntityNotFoundException("Source not found: client=" + clientId + ", sysPrin=" + sourceSysPrin);
    }

    int tgtCount = repo.countByKey(clientId, targetSysPrin);
    boolean inserted = false;
    int updated = 0;

    if (tgtCount == 0) {
      // Seed a single row for target by cloning TOP 1 from source
      int ins = repo.insertOneFromSource(clientId, sourceSysPrin, targetSysPrin);
      inserted = ins > 0;
    } else {
      if (!overwrite) {
        throw new IllegalStateException("Target already exists and overwrite=false: client=" + clientId + ", sysPrin=" + targetSysPrin);
      }
      // Overwrite ALL duplicates of target with source values
      updated += repo.overwriteTargetFromSource(clientId, sourceSysPrin, targetSysPrin);
    }

    int areas = 0;
    if (copyAreas) {
      areas = repo.copyAreas(sourceSysPrin, targetSysPrin);
    }

    return new DuplicateResult(clientId, sourceSysPrin, targetSysPrin, inserted, updated, areas);
  }
}
