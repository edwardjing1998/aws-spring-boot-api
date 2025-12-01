package your.package.repository;

import your.package.domain.SysPrin;
import your.package.domain.SysPrinId;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;
import org.springframework.data.jpa.repository.JpaRepository;

public interface SysPrinRepository extends JpaRepository<SysPrin, SysPrinId> {

    // existing:
    // List<SysPrin> findByIdClient(String client);

    // NEW: DB-level pagination
    Page<SysPrin> findByIdClient(String client, Pageable pageable);
}
