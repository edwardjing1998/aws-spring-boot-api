package rapid.model.cases;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;
import org.springframework.test.context.ContextConfiguration;
import rapid.CasesModelApplication;
import rapid.model.cases.address.Address;
import rapid.model.cases.cases.Case;
import rapid.model.cases.key.AddressId;
import rapid.repository.cases.address.AddressRepo;
import rapid.repository.cases.cases.CaseRepo;
import java.time.LocalDateTime;
import java.util.List;

import org.springframework.test.context.ActiveProfiles;
import static org.assertj.core.api.Assertions.assertThat;

@DataJpaTest(properties = {
        "spring.jpa.hibernate.ddl-auto=none",
        "spring.jpa.properties.hibernate.hbm2ddl.auto=none",
        "spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.H2Dialect"
})
@ContextConfiguration(classes = CasesModelApplication.class)
@ActiveProfiles("test")
class DltCaseAddressMappingTest {

    @Autowired
    private CaseRepo caseRepo;

    @Autowired
    AddressRepo addressRepo;


    @Test
    void caseAndAddress_arePersistedAndLinked() {

        /* ---------- create the parent entity ---------- */
        Case c = new Case();
        c.setCaseNumber("CASE000001");
        c.setPiId("PI-123");
        c.setAccount("1234567890123456");
        c.setActive(true);
        c.setInDate(LocalDateTime.now());

        /* ---------- create the child entity ---------- */
        AddressId addrId = new AddressId("CASE000001", "1"); // (caseNumber, sequence)
        Address addr   = new Address();
        addr.setId(addrId);
        addr.setAddr1("100 Main St.");
        addr.setCity("New York");
        addr.setState("NY");
        addr.setZip("10001");

        /* link both sides of the association */
        addr.setCaseEntity(c);
        c.getAddresses().add(addr);

        /* ---------- persist ---------- */
        caseRepo.saveAndFlush(c);

        /* ---------- verify ---------- */
        Case dbCase = caseRepo.findById("CASE000001").orElseThrow();
        assertThat(dbCase.getAddresses()).hasSize(1);

        Address dbAddr = dbCase.getAddresses().getFirst();
        assertThat(dbAddr.getCity()).isEqualTo("New York");
        assertThat(dbAddr.getCaseEntity()).isSameAs(dbCase);  // bidirectional ok
        assertThat(dbAddr.getAddr1()).isEqualTo("100 Main St.");
        assertThat(dbAddr.getCity()).isEqualTo("New York");
        assertThat(dbAddr.getZip()).isEqualTo("10001");
        assertThat(dbAddr.getState()).isEqualTo("NY");
    }

    @Test
    void repoMethod_isCovered() {
        Address addr   = new Address();
        AddressId addrId = new AddressId("CASE000001", "1"); // (caseNumber, sequence)
        addr.setId(addrId);
        addr.setAddr1("100 Main St.");
        addr.setCity("New York");
        addr.setState("NY");
        addr.setZip("10001");

        addressRepo.save(addr);                 // create
        List<Address> found = addressRepo
                .findByIdCaseNumberIdIn(List.of("CASE000001"));
        assertThat(found).hasSize(1);
    }
}
