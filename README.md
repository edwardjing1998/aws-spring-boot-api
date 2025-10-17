package rapid.model.cases;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.jdbc.AutoConfigureTestDatabase;
import org.springframework.boot.test.autoconfigure.jdbc.AutoConfigureTestDatabase.Replace;
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;
import org.springframework.test.context.ActiveProfiles;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.TestPropertySource;
import rapid.CasesModelApplication;
import rapid.model.cases.address.Address;
import rapid.model.cases.cases.Case;
import rapid.model.cases.key.AddressId;
import rapid.repository.cases.address.AddressRepo;
import rapid.repository.cases.cases.CaseRepo;

import java.time.LocalDateTime;
import java.util.List;

import static org.assertj.core.api.Assertions.assertThat;

@DataJpaTest
@AutoConfigureTestDatabase(replace = Replace.ANY)
@ActiveProfiles("test")
@ContextConfiguration(classes = CasesModelApplication.class)
// Key: disable Liquibase here and let Hibernate build an in-mem schema
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

    @Autowired private CaseRepo caseRepo;
    @Autowired private AddressRepo addressRepo;

    @Test
    void caseAndAddress_arePersistedAndLinked() {
        // parent
        Case c = new Case();
        c.setCaseNumber("CASE000001");
        c.setPiId("PI-123");
        c.setAccount("1234567890123456");
        c.setActive(true);
        c.setInDate(LocalDateTime.now());

        // child
        AddressId addrId = new AddressId("CASE000001", "1");
        Address addr = new Address();
        addr.setId(addrId);
        addr.setAddr1("100 Main St.");
        addr.setCity("New York");
        addr.setState("NY");
        addr.setZip("10001");

        // link
        addr.setCaseEntity(c);
        c.getAddresses().add(addr);

        // persist
        caseRepo.saveAndFlush(c);

        // verify
        Case dbCase = caseRepo.findById("CASE000001").orElseThrow();
        assertThat(dbCase.getAddresses()).hasSize(1);

        Address dbAddr = dbCase.getAddresses().getFirst();
        assertThat(dbAddr.getCity()).isEqualTo("New York");
        assertThat(dbAddr.getCaseEntity()).isSameAs(dbCase);
        assertThat(dbAddr.getAddr1()).isEqualTo("100 Main St.");
        assertThat(dbAddr.getZip()).isEqualTo("10001");
        assertThat(dbAddr.getState()).isEqualTo("NY");
    }

    @Test
    void repoMethod_isCovered() {
        // ensure parent exists due to FK
        Case c = new Case();
        c.setCaseNumber("CASE000001");
        c.setPiId("PI-123");
        c.setAccount("1234567890123456");
        c.setActive(true);
        c.setInDate(LocalDateTime.now());
        caseRepo.saveAndFlush(c);

        // child
        AddressId addrId = new AddressId("CASE000001", "1");
        Address addr = new Address();
        addr.setId(addrId);
        addr.setAddr1("100 Main St.");
        addr.setCity("New York");
        addr.setState("NY");
        addr.setZip("10001");
        addr.setCaseEntity(c);

        addressRepo.saveAndFlush(addr);

        List<Address> found = addressRepo.findByIdCaseNumberIdIn(List.of("CASE000001"));
        assertThat(found).hasSize(1);
    }
}
