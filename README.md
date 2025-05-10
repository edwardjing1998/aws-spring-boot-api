create-file-load-dest-table.xml


<?xml version="1.0" encoding="UTF-8"?>
<databaseChangeLog
        xmlns="http://www.liquibase.org/xml/ns/dbchangelog"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://www.liquibase.org/xml/ns/dbchangelog
                        http://www.liquibase.org/xml/ns/dbchangelog/dbchangelog-3.8.xsd">

    <changeSet id="create-file-load-dest-table" author="harish chander baswapuram">
        <createTable tableName="file_load_dest">
            <column name="report_id" type="INT" autoIncrement="true">
                <constraints primaryKey="true" nullable="false"/>
            </column>
            <column name="db_server" type="CHAR(25)">
                <constraints nullable="false"/>
            </column>
            <column name="db_driver" type="VARCHAR(50)">
                <constraints nullable="false"/>
            </column>
            <column name="user_id" type="CHAR(15)">
                <constraints nullable="true"/>
            </column>
            <column name="pass_word" type="VARCHAR(50)">
                <constraints nullable="false"/>
            </column>
            <column name="db_ip_and_port" type="VARCHAR(100)">
                <constraints nullable="false"/>
            </column>
            <column name="table_name" type="VARCHAR(50)">
                <constraints nullable="false"/>
            </column>
            <column name="data_base" type="VARCHAR(25)">
                <constraints nullable="false"/>
            </column>
        </createTable>
    </changeSet>

</databaseChangeLog>
