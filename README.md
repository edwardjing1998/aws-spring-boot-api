package rapid.repository.sysprin;

import org.springframework.data.jpa.repository.Modifying;
import org.springframework.data.jpa.repository.Query;
import org.springframework.data.repository.Repository;
import org.springframework.transaction.annotation.Transactional;

import java.util.List;
import java.util.Optional;

public interface SysPrinNativeRepository extends Repository<Object, Long> {

  interface TemplateRow {
    String getCUST_TYPE();
    String getUNDELIVERABLE();
    String getSTAT_A();
    String getSTAT_B();
    String getSTAT_C();
    String getSTAT_D();
    String getSTAT_E();
    String getSTAT_F();
    String getSTAT_I();
    String getSTAT_L();
    String getSTAT_O();
    String getSTAT_U();
    String getSTAT_X();
    String getSTAT_Z();

    String getPO_BOX();
    String getADDR_FLAG();
    Long   getTEMP_AWAY();
    String getRPS();
    String getSESSION();
    String getBAD_STATE();
    String getA_STAT_RCH();
    String getNM_13();
    Long   getTEMP_AWAY_ATTS();
    Double getREPORT_METHOD();
    Short  getACTIVE();
    String getNOTES();
    String getRET_STAT();
    String getDES_STAT();
    String getNON_US();
    String getSPECIAL();
    String getPIN();
    String getFORWARDING_ADDR();
    Integer getHOLD_DAYS();
    String getCONTACT();
    String getPHONE();
    String getENTITY_CD();
  }

  @Query(value = """
      SELECT TOP 1
        CUST_TYPE, UNDELIVERABLE, STAT_A, STAT_B, STAT_C, STAT_D, STAT_E, STAT_F,
        STAT_I, STAT_L, STAT_O, STAT_U, STAT_X, STAT_Z,
        PO_BOX, ADDR_FLAG, TEMP_AWAY, RPS, SESSION, BAD_STATE, A_STAT_RCH, NM_13,
        TEMP_AWAY_ATTS, REPORT_METHOD, ACTIVE, NOTES, RET_STAT, DES_STAT, NON_US,
        SPECIAL, PIN, FORWARDING_ADDR, HOLD_DAYS, CONTACT, PHONE, ENTITY_CD
      FROM sys_prins
      WHERE client = :client AND sys_prin = :sysPrin
      """, nativeQuery = true)
  Optional<TemplateRow> findTemplateRow(String client, String sysPrin);

  @Query(value = """
      SELECT DISTINCT sys_prin
      FROM sys_prins
      WHERE client = :client
      """, nativeQuery = true)
  List<String> findDistinctSysPrinsByClient(String client);

  @Modifying(clearAutomatically = true, flushAutomatically = true)
  @Transactional
  @Query(value = """
      UPDATE sys_prins SET
        CUST_TYPE = :custType,
        UNDELIVERABLE = :undeliverable,
        STAT_A = :statA,
        STAT_B = :statB,
        STAT_C = :statC,
        STAT_D = :statD,
        STAT_E = :statE,
        STAT_F = :statF,
        STAT_I = :statI,
        STAT_L = :statL,
        STAT_O = :statO,
        STAT_U = :statU,
        STAT_X = :statX,
        STAT_Z = :statZ,
        PO_BOX = :poBox,
        ADDR_FLAG = :addrFlag,
        TEMP_AWAY = :tempAway,
        RPS = :rps,
        SESSION = :sessionVal,
        BAD_STATE = :badState,
        A_STAT_RCH = :astatRch,
        NM_13 = :nm13,
        TEMP_AWAY_ATTS = :tempAwayAtts,
        REPORT_METHOD = :reportMethod,
        ACTIVE = :active,
        NOTES = :notes,
        RET_STAT = :retStat,
        DES_STAT = :desStat,
        NON_US = :nonUS,
        SPECIAL = :special,
        PIN = :pin,
        FORWARDING_ADDR = :forwardingAddr,
        HOLD_DAYS = :holdDays,
        CONTACT = :contact,
        PHONE = :phone,
        ENTITY_CD = :entityCd
      WHERE client = :client AND sys_prin = :sysPrin
      """, nativeQuery = true)
  int updateSettingsForKey(
      String client, String sysPrin,
      String custType, String undeliverable,
      String statA, String statB, String statC, String statD, String statE, String statF,
      String statI, String statL, String statO, String statU, String statX, String statZ,
      String poBox, String addrFlag, Long tempAway, String rps, String sessionVal, String badState,
      String astatRch, String nm13, Long tempAwayAtts, Double reportMethod, Short active,
      String notes, String retStat, String desStat, String nonUS, String special,
      String pin, String forwardingAddr, Integer holdDays, String contact, String phone,
      String entityCd
  );
}
