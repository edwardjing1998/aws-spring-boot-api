// rapid/model/cases/TestApp.java
@SpringBootApplication
@ActiveProfiles("test")
public class TestApp {
  @Bean
  public JdbcTemplate jdbcTemplate(DataSource dataSource) {
    return new JdbcTemplate(dataSource);
  }
}
