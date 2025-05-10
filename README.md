<?xml version="1.0" encoding="UTF-8"?>
<databaseChangeLog
        xmlns="http://www.liquibase.org/xml/ns/dbchangelog"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://www.liquibase.org/xml/ns/dbchangelog
                        http://www.liquibase.org/xml/ns/dbchangelog/dbchangelog-3.8.xsd">

    <changeSet id="create-admin-data-def-list" author="harish chander baswapuram">
        <createTable tableName="admin_query_data_def">
            <column name="report_id" type="SMALLINT" autoIncrement="true">
                <constraints primaryKey="true" nullable="false"/>
            </column>
            <column name="input0_data_mask" type="VARCHAR(50)">
                <constraints nullable="false"/>
            </column>
            <column name="input0_data_type" type="VARCHAR(5)">
                <constraints nullable="true"/>
            </column>
            <column name="input0_data_length" type="VARCHAR(5)">
                <constraints nullable="false"/>
            </column>
            <column name="input1_data_mask" type="VARCHAR(50)">
                <constraints nullable="false"/>
            </column>
            <column name="input1_data_type" type="VARCHAR(5)">
                <constraints nullable="true"/>
            </column>
            <column name="input1_data_length" type="VARCHAR(5)">
                <constraints nullable="false"/>
            </column>
            <column name="input2_data_mask" type="VARCHAR(50)">
                <constraints nullable="false"/>
            </column>
            <column name="input2_data_type" type="VARCHAR(5)">
                <constraints nullable="true"/>
            </column>
            <column name="input2_data_length" type="VARCHAR(5)">
                <constraints nullable="false"/>
            </column>
            <column name="input3_data_mask" type="VARCHAR(50)">
                <constraints nullable="false"/>
            </column>
            <column name="input3_data_type" type="VARCHAR(5)">
                <constraints nullable="true"/>
            </column>
            <column name="input3_data_length" type="VARCHAR(5)">
                <constraints nullable="false"/>
            </column>
            <column name="input4_data_mask" type="VARCHAR(50)">
                <constraints nullable="false"/>
            </column>
            <column name="input4_data_type" type="VARCHAR(5)">
                <constraints nullable="true"/>
            </column>
            <column name="input4_data_length" type="VARCHAR(5)">
                <constraints nullable="false"/>
            </column>
            <column name="input5_data_mask" type="VARCHAR(50)">
                <constraints nullable="false"/>
            </column>
            <column name="input5_data_type" type="VARCHAR(5)">
                <constraints nullable="true"/>
            </column>
            <column name="input5_data_length" type="VARCHAR(5)">
                <constraints nullable="false"/>
            </column>
            <column name="input6_data_mask" type="VARCHAR(50)">
                <constraints nullable="false"/>
            </column>
            <column name="input6_data_type" type="VARCHAR(5)">
                <constraints nullable="true"/>
            </column>
            <column name="input6_data_length" type="VARCHAR(5)">
                <constraints nullable="false"/>
            </column>
            <column name="input7_data_mask" type="VARCHAR(50)">
                <constraints nullable="false"/>
            </column>
            <column name="input7_data_type" type="VARCHAR(5)">
                <constraints nullable="true"/>
            </column>
            <column name="input7_data_length" type="VARCHAR(5)">
                <constraints nullable="false"/>
            </column>
            <column name="input8_data_mask" type="VARCHAR(50)">
                <constraints nullable="false"/>
            </column>
            <column name="input8_data_type" type="VARCHAR(5)">
                <constraints nullable="true"/>
            </column>
            <column name="input8_data_length" type="VARCHAR(5)">
                <constraints nullable="false"/>
            </column>
        </createTable>
    </changeSet>
</databaseChangeLog>


create-admin-query-data-def-table.xml
