package trace.web.sysprin;

import lombok.RequiredArgsConstructor;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;
import org.springframework.data.web.PageableDefault;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;
import trace.dto.sysPrin.VendorReceivedFromDTO;
import trace.service.sysprin.VendorReceivedFromDtoService;

@RestController
@RequestMapping("/api/vendor-received-from")
@RequiredArgsConstructor
public class VendorReceivedFromDtoController {

    private final VendorReceivedFromDtoService service;

    /**
     * Example:
     * GET /api/vendor-received-from/57491000/dto?page=0&size=10
     */
    @GetMapping("/{sysPrin}/dto")
    public ResponseEntity<Page<VendorReceivedFromDTO>> getBySysPrinDto(
            @PathVariable String sysPrin,
            @PageableDefault(size = 10) Pageable pageable
    ) {
        return ResponseEntity.ok(service.getReceivedFromDtosBySysPrin(sysPrin, pageable));
    }
}
