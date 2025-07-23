<?xml version="1.0" encoding="UTF-8"?>
<databaseChangeLog
        xmlns="http://www.liquibase.org/xml/ns/dbchangelog"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://www.liquibase.org/xml/ns/dbchangelog
                        http://www.liquibase.org/xml/ns/dbchangelog/dbchangelog-3.8.xsd">

    <changeSet id="create-admin-query-list" author="harish chander baswapuram">
        <createTable tableName="ADMIN_QUERY_TOOL_TIPS">
            <column name="report_id" type="INT" autoIncrement="true">
                <constraints primaryKey="true" nullable="false"/>
            </column>
            <column name="tool_tip0_tx" type="VARCHAR(50)">
                <constraints nullable="true"/>
            </column>
            <column name="tool_tip1_tx" type="VARCHAR(50)">
                <constraints nullable="true"/>
            </column>
            <column name="tool_tip2_tx" type="VARCHAR(50)">
                <constraints nullable="true"/>
            </column>
            <column name="tool_tip3_tx" type="VARCHAR(50)">
                <constraints nullable="true"/>
            </column>
            <column name="tool_tip4_tx" type="VARCHAR(50)">
                <constraints nullable="true"/>
            </column>
            <column name="tool_tip5_tx" type="VARCHAR(50)">
                <constraints nullable="true"/>
            </column>
            <column name="tool_tip6_tx" type="VARCHAR(50)">
                <constraints nullable="true"/>
            </column>
            <column name="tool_tip7_tx" type="VARCHAR(50)">
                <constraints nullable="true"/>
            </column>
            <column name="tool_tip8_tx" type="VARCHAR(50)">
                <constraints nullable="true"/>
            </column>
        </createTable>
    </changeSet>
</databaseChangeLog>



    <include file="db/changelog/create-admin-query-tool-tips.xml"/>






