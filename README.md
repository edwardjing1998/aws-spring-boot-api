package your.package.repository;

import your.package.domain.VendorReceivedFrom;
import your.package.domain.VendorReceivedFromId;
import org.springframework.data.jpa.repository.JpaRepository;

import java.util.Collection;
import java.util.List;

public interface VendorReceivedFromRepository extends JpaRepository<VendorReceivedFrom, VendorReceivedFromId> {

    // NEW: only rows for given sysPrins
    List<VendorReceivedFrom> findByIdSysPrinIn(Collection<String> sysPrins);
}
