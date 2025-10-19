package rapid.model.sysprin.base;

import jakarta.persistence.*;
import lombok.Data;
import rapid.model.sysprin.key.SysPrinId;

@MappedSuperclass
@Data
public class BaseSysPrin {

    @EmbeddedId
    private SysPrinId id;

    @Column(name = "CUST_TYPE") private String custType;
    @Column(name = "UNDELIVERABLE") private String undeliverable;
    @Column(name = "STAT_A") private String statA;
    @Column(name = "STAT_B") private String statB;
    @Column(name = "STAT_C") private String statC;
    @Column(name = "STAT_D") private String statD;
    @Column(name = "STAT_E") private String statE;
    @Column(name = "STAT_F") private String statF;
    @Column(name = "STAT_I") private String statI;
    @Column(name = "STAT_L") private String statL;
    @Column(name = "STAT_O") private String statO;
    @Column(name = "STAT_U") private String statU;
    @Column(name = "STAT_X") private String statX;
    @Column(name = "STAT_Z") private String statZ;

    @Column(name = "PO_BOX") private String poBox;
    @Column(name = "ADDR_FLAG") private String addrFlag;
    @Column(name = "TEMP_AWAY") private Long tempAway;
    @Column(name = "RPS") private String rps;
    @Column(name = "SESSION") private String session;
    @Column(name = "BAD_STATE") private String badState;
    @Column(name = "A_STAT_RCH") private String astatRch;
    @Column(name = "NM_13") private String nm13;
    @Column(name = "TEMP_AWAY_ATTS") private Long tempAwayAtts;
    @Column(name = "REPORT_METHOD") private Double reportMethod;
    @Column(name = "ACTIVE") private Short active;
    @Column(name = "NOTES") private String notes;
    @Column(name = "RET_STAT") private String returnStatus;
    @Column(name = "DES_STAT") private String destroyStatus;
    @Column(name = "NON_US") private String nonUS;
    @Column(name = "SPECIAL") private String special;
    @Column(name = "PIN") private String pinMailer;
    @Column(name = "FORWARDING_ADDR") private String forwardingAddress;
    @Column(name = "HOLD_DAYS") private Integer holdDays;
    @Column(name = "CONTACT") private String contact;
    @Column(name = "PHONE") private String phone;
    @Column(name = "ENTITY_CD") private String entityCode;
}





SELECT
  COLUMN_NAME,
  DATA_TYPE,
  CHARACTER_MAXIMUM_LENGTH,
  NUMERIC_PRECISION,
  NUMERIC_SCALE,
  IS_NULLABLE
FROM INFORMATION_SCHEMA.COLUMNS
WHERE TABLE_NAME = 'sys_prins'      -- your table
  AND COLUMN_NAME = 'ACTIVE';       -- your column

