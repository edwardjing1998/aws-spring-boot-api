package your.package.repository;

import your.package.domain.InvalidDelivArea;
import your.package.domain.InvalidDelivAreaId;
import org.springframework.data.jpa.repository.JpaRepository;

import java.util.Collection;
import java.util.List;

public interface InvalidDelivAreaRepository extends JpaRepository<InvalidDelivArea, InvalidDelivAreaId> {

    // NEW: only areas for given sysPrins
    List<InvalidDelivArea> findByIdSysPrinIn(Collection<String> sysPrins);
}
