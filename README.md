package your.package.repository;

import your.package.domain.VendorSentTo;
import your.package.domain.VendorSentToId;
import org.springframework.data.jpa.repository.JpaRepository;

import java.util.Collection;
import java.util.List;

public interface VendorSentToRepository extends JpaRepository<VendorSentTo, VendorSentToId> {

    // NEW: only rows for given sysPrins
    List<VendorSentTo> findByIdSysPrinIn(Collection<String> sysPrins);
}
