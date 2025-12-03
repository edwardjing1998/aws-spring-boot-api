import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Modifying;
import org.springframework.data.jpa.repository.Query;
import org.springframework.transaction.annotation.Transactional;

// Assuming your concrete entity class is named VendorSentTo
public interface VendorSentToRepository extends JpaRepository<VendorSentTo, VendorSentToId> {

    /** Copy vendor_sent_to from source to target, deduped. */
    @Modifying(clearAutomatically = true, flushAutomatically = true)
    @Transactional
    @Query(value = """
      INSERT INTO VENDOR_SENT_TO (SYS_PRIN, VEND_ID, QUEFORMAIL_CD)
      SELECT :targetSysPrin, src.VEND_ID, src.QUEFORMAIL_CD
      FROM VENDOR_SENT_TO src
      WHERE src.SYS_PRIN = :sourceSysPrin
        AND NOT EXISTS (
          SELECT 1
          FROM VENDOR_SENT_TO x
          WHERE x.SYS_PRIN = :targetSysPrin
            AND x.VEND_ID = src.VEND_ID
        )
      """, nativeQuery = true)
    int copyVendorSentTo(String sourceSysPrin, String targetSysPrin);
}
