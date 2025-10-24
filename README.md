package rapid.repository.sysprin;

import jakarta.transaction.Transactional;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Modifying;
import org.springframework.data.jpa.repository.Query;
import org.springframework.data.repository.query.Param;
import rapid.model.sysprin.InvalidDelivArea;
import rapid.model.sysprin.key.InvalidDelivAreaId;

import java.util.List;
import java.util.Set;

public interface InvalidDelivAreaRepository extends JpaRepository<InvalidDelivArea, Long> {

    @Modifying
    @Transactional
    @Query("DELETE FROM InvalidDelivArea i WHERE i.id.sysPrin = :sysPrin")
    void deleteBySysPrin(@Param("sysPrin") String sysPrin);

    List<InvalidDelivArea> findAllByIdSysPrin(String sysPrin);


    @Query("""
         select a.id.area
         from InvalidDelivArea a
         where a.id.sysPrin = :sysPrin
         """)
    Set<String> findAreasBySysPrin(String sysPrin);

    default int bulkInsertAreas(String targetSysPrin, List<String> areas) {
        if (areas == null || areas.isEmpty()) return 0;

        var entities = areas.stream().map(area -> {
            InvalidDelivArea e = new InvalidDelivArea();        // uses @NoArgsConstructor
            e.setId(new InvalidDelivAreaId(targetSysPrin, area)); // set EmbeddedId
            return e;
        }).toList();

        saveAll(entities);
        return entities.size();
    }




}
