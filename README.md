// src/main/java/rapid/repository/sysprin/SysPrinDuplicateNativeRepository.java
package rapid.repository.sysprin;

import org.springframework.data.jpa.repository.*;
import org.springframework.transaction.annotation.Transactional;
import rapid.model.sysprin.SysPrin;
import rapid.model.sysprin.key.SysPrinId;

public interface SysPrinDuplicateNativeRepository extends JpaRepository<SysPrin, SysPrinId> {

  /** Count physical rows for key (tolerates legacy duplicates). */
  @Query(value = """
      SELECT COUNT(*) FROM sys_prins
      WHERE client = :client AND sys_prin = :sysPrin
      """, nativeQuery = true)
  int countByKey(String client, String sysPrin);

  /** Insert one target row by selecting a single (TOP 1) source row. (SQL Server) */
  @Modifying(clearAutomatically = true, flushAutomatically = true)
  @Transactional
  @Query(value = """
      INSERT INTO sys_prins(
        client, sys_prin,
        CUST_TYPE, UNDELIVERABLE, STAT_A, STAT_B, STAT_C, STAT_D, STAT_E, STAT_F,
        STAT_I, STAT_L, STAT_O, STAT_U, STAT_X, STAT_Z,
        PO_BOX, ADDR_FLAG, TEMP_AWAY, RPS, SESSION, BAD_STATE, A_STAT_RCH, NM_13,
        TEMP_AWAY_ATTS, REPORT_METHOD, ACTIVE, NOTES, RET_STAT, DES_STAT, NON_US,
        SPECIAL, PIN, FORWARDING_ADDR, HOLD_DAYS, CONTACT, PHONE, ENTITY_CD
      )
      SELECT TOP 1
        :client            AS client,
        :targetSysPrin     AS sys_prin,
        s.CUST_TYPE, s.UNDELIVERABLE, s.STAT_A, s.STAT_B, s.STAT_C, s.STAT_D, s.STAT_E, s.STAT_F,
        s.STAT_I, s.STAT_L, s.STAT_O, s.STAT_U, s.STAT_X, s.STAT_Z,
        s.PO_BOX, s.ADDR_FLAG, s.TEMP_AWAY, s.RPS, s.SESSION, s.BAD_STATE, s.A_STAT_RCH, s.NM_13,
        s.TEMP_AWAY_ATTS, s.REPORT_METHOD, s.ACTIVE, s.NOTES, s.RET_STAT, s.DES_STAT, s.NON_US,
        s.SPECIAL, s.PIN, s.FORWARDING_ADDR, s.HOLD_DAYS, s.CONTACT, s.PHONE, s.ENTITY_CD
      FROM sys_prins s
      WHERE s.client = :client AND s.sys_prin = :sourceSysPrin
      """, nativeQuery = true)
  int insertOneFromSource(String client, String sourceSysPrin, String targetSysPrin);

  /** Overwrite ALL target-duplicate rows with source settings (bulk UPDATE). */
  @Modifying(clearAutomatically = true, flushAutomatically = true)
  @Transactional
  @Query(value = """
      UPDATE t SET
        CUST_TYPE = s.CUST_TYPE,
        UNDELIVERABLE = s.UNDELIVERABLE,
        STAT_A = s.STAT_A, STAT_B = s.STAT_B, STAT_C = s.STAT_C,
        STAT_D = s.STAT_D, STAT_E = s.STAT_E, STAT_F = s.STAT_F,
        STAT_I = s.STAT_I, STAT_L = s.STAT_L, STAT_O = s.STAT_O,
        STAT_U = s.STAT_U, STAT_X = s.STAT_X, STAT_Z = s.STAT_Z,
        PO_BOX = s.PO_BOX, ADDR_FLAG = s.ADDR_FLAG, TEMP_AWAY = s.TEMP_AWAY,
        RPS = s.RPS, SESSION = s.SESSION, BAD_STATE = s.BAD_STATE, A_STAT_RCH = s.A_STAT_RCH,
        NM_13 = s.NM_13, TEMP_AWAY_ATTS = s.TEMP_AWAY_ATTS, REPORT_METHOD = s.REPORT_METHOD,
        ACTIVE = s.ACTIVE, NOTES = s.NOTES, RET_STAT = s.RET_STAT, DES_STAT = s.DES_STAT,
        NON_US = s.NON_US, SPECIAL = s.SPECIAL, PIN = s.PIN, FORWARDING_ADDR = s.FORWARDING_ADDR,
        HOLD_DAYS = s.HOLD_DAYS, CONTACT = s.CONTACT, PHONE = s.PHONE, ENTITY_CD = s.ENTITY_CD
      FROM sys_prins t
      JOIN (SELECT TOP 1 * FROM sys_prins
            WHERE client = :client AND sys_prin = :sourceSysPrin) s
        ON 1=1
      WHERE t.client = :client AND t.sys_prin = :targetSysPrin
      """, nativeQuery = true)
  int overwriteTargetFromSource(String client, String sourceSysPrin, String targetSysPrin);

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
}
