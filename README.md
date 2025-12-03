package rapid.model.sysprin.base;

import jakarta.persistence.Column;
import jakarta.persistence.EmbeddedId;
import jakarta.persistence.MappedSuperclass;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import rapid.model.sysprin.key.VendorSentToId;

@MappedSuperclass
@Data
@AllArgsConstructor
@NoArgsConstructor
public abstract class BaseVendorSentTo {

    @EmbeddedId
    private VendorSentToId id;

    @Column(name = "queformail_cd", nullable = false)
    private Boolean queForMail;
}







package rapid.model.sysprin.key;

import jakarta.persistence.Column;
import jakarta.persistence.Embeddable;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

import java.io.Serializable;

@Embeddable
@Data
@NoArgsConstructor
@AllArgsConstructor
public class VendorSentToId implements Serializable {

    @Column(name = "vend_id", length = 3, nullable = false)
    private String vendorId;

    @Column(name = "sys_prin", length = 12, nullable = false)
    private String sysPrin;


}








 /** Copy invalid_deliv_areas from source to target, deduped. */
    @Modifying(clearAutomatically = true, flushAutomatically = true)
    @Transactional
    @Query(value = """
      INSERT INTO INVALID_DELIV_AREAS (SYS_PRIN, AREA)
      SELECT :targetSysPrin, src.AREA
      FROM INVALID_DELIV_AREAS src
      WHERE src.SYS_PRIN = :sourceSysPrin
        AND NOT EXISTS (
          SELECT 1
          FROM INVALID_DELIV_AREAS x
          WHERE x.SYS_PRIN = :targetSysPrin
            AND x.AREA = src.AREA
        )
      """, nativeQuery = true)
    int copyAreas(String sourceSysPrin, String targetSysPrin);







