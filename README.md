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








<?xml version="1.0" encoding="UTF-8"?>
<databaseChangeLog
        xmlns="http://www.liquibase.org/xml/ns/dbchangelog"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://www.liquibase.org/xml/ns/dbchangelog
        http://www.liquibase.org/xml/ns/dbchangelog/dbchangelog-4.3.xsd">

    <changeSet id="create-sys-prins-table" author="Edward Jing">

        <createTable tableName="SYS_PRINS">
            <column name="CLIENT" type="VARCHAR(4)">
                <constraints nullable="false"/>
            </column>
            <column name="SYS_PRIN" type="VARCHAR(12)">
                <constraints nullable="false"/>
            </column>

            <column name="CUST_TYPE" type="VARCHAR(1)"/>
            <column name="UNDELIVERABLE" type="VARCHAR(255)"/>
            <column name="STAT_A" type="VARCHAR(1)"/>
            <column name="STAT_B" type="VARCHAR(1)"/>
            <column name="STAT_C" type="VARCHAR(1)"/>
            <column name="STAT_D" type="VARCHAR(1)"/>
            <column name="STAT_E" type="VARCHAR(1)"/>
            <column name="STAT_F" type="VARCHAR(1)"/>
            <column name="STAT_I" type="VARCHAR(1)"/>
            <column name="STAT_L" type="VARCHAR(1)"/>
            <column name="STAT_O" type="VARCHAR(1)"/>
            <column name="STAT_U" type="VARCHAR(1)"/>
            <column name="STAT_X" type="VARCHAR(1)"/>
            <column name="STAT_Z" type="VARCHAR(1)"/>
            <column name="PO_BOX" type="VARCHAR(1)"/>
            <column name="ADDR_FLAG" type="VARCHAR(1)"/>
            <column name="TEMP_AWAY" type="BIGINT"/>
            <column name="RPS" type="VARCHAR(1)"/>
            <column name="SESSION" type="VARCHAR(1)"/>
            <column name="BAD_STATE" type="VARCHAR(1)"/>
            <column name="A_STAT_RCH" type="VARCHAR(1)"/>
            <column name="NM_13" type="VARCHAR(1)"/>
            <column name="TEMP_AWAY_ATTS" type="BIGINT"/>
            <column name="REPORT_METHOD" type="FLOAT"/>
            <column name="ACTIVE" type="SMALLINT"/>
            <column name="NOTES" type="VARCHAR(255)"/>
            <column name="RET_STAT" type="VARCHAR(1)"/>
            <column name="DES_STAT" type="VARCHAR(1)"/>
            <column name="NON_US" type="VARCHAR(1)"/>
            <column name="SPECIAL" type="VARCHAR(1)"/>
            <column name="PIN" type="VARCHAR(1)"/>
            <column name="HOLD_DAYS" type="BIGINT"/>
            <column name="FORWARDING_ADDR" type="VARCHAR(1)"/>

            <column name="CONTACT" type="VARCHAR(20)"/>
            <column name="PHONE" type="VARCHAR(13)"/>
            <column name="ENTITY_CD" type="VARCHAR(1)"/>
        </createTable>

    </changeSet>
</databaseChangeLog>
