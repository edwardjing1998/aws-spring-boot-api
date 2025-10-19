package rapid.repository.sysprin;

import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Query;
import rapid.model.sysprin.InvalidDelivArea;
import rapid.model.sysprin.key.InvalidDelivAreaId;

import java.util.List;
import java.util.Set;

public interface InvalidDelivAreaRepository extends JpaRepository<InvalidDelivArea, InvalidDelivAreaId> {

  @Query("""
         select a.id.area
         from InvalidDelivArea a
         where a.id.sysPrin = :sysPrin
         """)
  Set<String> findAreasBySysPrin(String sysPrin);

  /**
   * Portable batch insert via saveAll(). Caller should dedupe input first
   * (we already do NOT EXISTS checks in the service), but a unique constraint
   * on (sys_prin, area) is still recommended.
   */
  default int bulkInsertAreas(String targetSysPrin, List<String> areas) {
    if (areas == null || areas.isEmpty()) return 0;
    var entities = areas.stream()
        .map(a -> new InvalidDelivArea(new InvalidDelivAreaId(targetSysPrin, a)))
        .toList();
    saveAll(entities);
    return entities.size();
  }
}
