package rapid.service.sysprin;

import jakarta.transaction.Transactional;
import org.springframework.stereotype.Service;
import org.springframework.util.StringUtils;
import rapid.model.sysprin.InvalidDelivArea;
import rapid.model.sysprin.key.InvalidDelivAreaId;
import rapid.repository.sysprin.InvalidDelivAreaRepository;

@Service
public class InvalidDelivAreaService {

    private final InvalidDelivAreaRepository repository;

    public InvalidDelivAreaService(InvalidDelivAreaRepository repository) {
        this.repository = repository;
    }

    @Transactional
    public InvalidDelivArea addArea(String sysPrin, String area) {
        if (!StringUtils.hasText(sysPrin)) {
            throw new IllegalArgumentException("sysPrin is required.");
        }
        if (!StringUtils.hasText(area)) {
            throw new IllegalArgumentException("area is required.");
        }

        var id = new InvalidDelivAreaId(sysPrin.trim(), area.trim());

        // Prevent duplicates gracefully
        if (repository.existsByIdSysPrinAndIdArea(id.getSysPrin(), id.getArea())) {
            // You can either:
            // 1) return the existing entity, or
            // 2) throw an exception. Here we return an entity-like value.
            var existing = new InvalidDelivArea();
            existing.setId(id);
            return existing;
        }

        var entity = new InvalidDelivArea();
        entity.setId(id);
        return repository.save(entity);
    }
}
