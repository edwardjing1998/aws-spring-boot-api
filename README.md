@DataJpaTest
@AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.ANY)
@EntityScan(basePackageClasses = { rapid.model.cases.cases.Case.class, rapid.model.cases.address.Address.class })
@EnableJpaRepositories(basePackageClasses = { rapid.repository.cases.cases.CaseRepo.class, rapid.repository.cases.address.AddressRepo.class })
@ActiveProfiles("test")
@TestPropertySource(properties = {
    "spring.liquibase.enabled=false",
    "spring.jpa.hibernate.ddl-auto=create-drop",
    "spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.H2Dialect",
    "spring.datasource.url=jdbc:h2:mem:cases;MODE=MSSQLServer;DB_CLOSE_DELAY=-1;DATABASE_TO_LOWER=TRUE",
    "spring.datasource.driverClassName=org.h2.Driver",
    "spring.datasource.username=sa",
    "spring.datasource.password="
})
class DltCaseAddressMappingTest {
