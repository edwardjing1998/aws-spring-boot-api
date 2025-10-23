package rapid.repository.sysprin;

import org.springframework.data.jpa.repository.Modifying;
import org.springframework.data.jpa.repository.Query;
import org.springframework.data.repository.Repository;
import org.springframework.transaction.annotation.Transactional;
import rapid.model.sysprin.InvalidDelivArea;

public interface InvalidDelivAreaNativeRepository extends Repository<InvalidDelivArea, Long> {

    @Modifying(clearAutomatically = true, flushAutomatically = true)
    @Transactional
    @Query(value = """
      INSERT INTO INVALID_DELIV_AREAS (SYS_PRIN, AREA)
      SELECT :targetSysPrin, src.AREA
      FROM INVALID_DELIV_AREAS src
      WHERE src.SYS_PRIN = :templateSysPrin
        AND NOT EXISTS (
          SELECT 1
          FROM INVALID_DELIV_AREAS x
          WHERE x.SYS_PRIN = :targetSysPrin
            AND x.AREA = src.AREA
        )
      """, nativeQuery = true)
    int copyAreasFromTemplate(String templateSysPrin, String targetSysPrin);
}
