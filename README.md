package rapid.model.cases;

import org.junit.jupiter.api.Test;
import org.springframework.boot.test.context.runner.ApplicationContextRunner;
import org.springframework.context.annotation.Bean;
import org.springframework.boot.test.context.TestConfiguration;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.test.context.ActiveProfiles;

import javax.sql.DataSource;

@ActiveProfiles("test")
class LiquibaseValidationTest {

  // Local test-only config. NOT @SpringBootApplication.
  @TestConfiguration
  static class LiquibaseTestConfig {
    @Bean
    JdbcTemplate jdbcTemplate(DataSource dataSource) {
      return new JdbcTemplate(dataSource);
    }
  }

  private final ApplicationContextRunner contextRunner = new ApplicationContextRunner()
      .withUserConfiguration(LiquibaseTestConfig.class)
      .withPropertyValues(
          "spring.datasource.url=jdbc:h2:mem:testdb;DB_CLOSE_DELAY=-1",
          "spring.datasource.driver-class-name=org.h2.Driver",
          "spring.datasource.username=sa",
          "spring.datasource.password=",
          "spring.liquibase.enabled=true",
          "spring.liquibase.change-log=classpath:db/changelog/db.changelog-master.xml"
      );

  private final String[] expectedTables = {
      "DELETED_FAILED_TRANS",
      "DELETED_ACCOUNT_TRANS",
      "DELETED_ADDRESSES",
      "DELETED_BULK_CARD",
      "DELETED_CASES",
      "DELETED_LABELS",
      "DELETED_TRANSACTIONS"
  };

  @Test
  void liquibaseShouldCreateAllTables() {
    contextRunner.run(context -> {
      JdbcTemplate jdbcTemplate = context.getBean(JdbcTemplate.class);
      for (String table : expectedTables) {
        Boolean exists = jdbcTemplate.queryForObject(
            "SELECT COUNT(*) > 0 FROM INFORMATION_SCHEMA.TABLES WHERE TABLE_NAME = ?",
            Boolean.class,
            table
        );
        org.assertj.core.api.Assertions.assertThat(exists)
          .as("Table " + table + " should exist")
          .isTrue();
      }
    });
  }
}
