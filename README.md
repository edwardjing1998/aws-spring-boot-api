package rapid.api.sysprin;

import jakarta.validation.Valid;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;
import org.springframework.web.servlet.support.ServletUriComponentsBuilder;
import rapid.api.sysprin.dto.InvalidDelivAreaCreateRequest;
import rapid.model.sysprin.InvalidDelivArea;
import rapid.service.sysprin.InvalidDelivAreaService;

@RestController
@RequestMapping("/api/sysprins/{sysPrin}/invalid-areas")
public class InvalidDelivAreaController {

    private final InvalidDelivAreaService service;

    public InvalidDelivAreaController(InvalidDelivAreaService service) {
        this.service = service;
    }

    @PostMapping
    public ResponseEntity<InvalidDelivArea> create(
            @PathVariable String sysPrin,
            @Valid @RequestBody InvalidDelivAreaCreateRequest request
    ) {
        var saved = service.addArea(sysPrin, request.area());

        var location = ServletUriComponentsBuilder
                .fromCurrentRequest()
                .path("/{area}")
                .buildAndExpand(saved.getId().getArea())
                .toUri();

        return ResponseEntity.created(location).body(saved);
    }
}
