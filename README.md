package your.package.web;

import your.package.dto.SysPrinDTO;
import your.package.dto.PageResponse;
import your.package.service.SysPrinService;

import jakarta.validation.constraints.Size;
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;

import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/api/sysprins")
@RequiredArgsConstructor
@Slf4j
public class SysPrinController {

    private final SysPrinService sysPrinService;

    // existing /client/{client} unchanged...

    @GetMapping("/client/{client}/paged")
    public ResponseEntity<PageResponse<SysPrinDTO>> getByClientPaged(
            @PathVariable
            @Size(max = 4, message = "ClientId should not be more than 4 characters")
            String client,
            @RequestParam(name = "page", defaultValue = "0") int page,
            @RequestParam(name = "size", defaultValue = "20") int size) {

        log.info("Paged SysPrin request: client={}, page={}, size={}", client, page, size);

        PageResponse<SysPrinDTO> response = sysPrinService.getByClientPaged(client, page, size);

        log.info("Returning {} SysPrins for client={}, page={} of {}",
                response.getContent().size(),
                client,
                response.getPage(),
                response.getTotalPages());

        return ResponseEntity.ok(response);
    }
}
