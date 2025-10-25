package rapid.service.sysprin;

import jakarta.persistence.EntityExistsException;
import jakarta.persistence.EntityNotFoundException;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;
import rapid.model.sysprin.key.VendorSentToId;
import rapid.model.sysprin.vendor.VendorReceivedFrom;
import rapid.repository.sysprin.VendorReceivedFromRepository;

@Service
@RequiredArgsConstructor
public class VendorReceivedFromService {

    private final VendorReceivedFromRepository repo;

    @Transactional
    public VendorReceivedFrom add(String sysPrin, String vendorId, Boolean queForMail) {
        var id = new VendorSentToId(vendorId, sysPrin);
        if (repo.existsById(id)) {
            throw new EntityExistsException("VendorReceivedFrom already exists for sysPrin="
                    + sysPrin + ", vendorId=" + vendorId);
        }
        // Option A: via save(...)
        return repo.addOne(sysPrin, vendorId, Boolean.TRUE.equals(queForMail));

        // Option B (native insert), if you prefer:
        // int rows = repo.insertOneNative(sysPrin, vendorId, Boolean.TRUE.equals(queForMail));
        // if (rows != 1) throw new IllegalStateException("Insert failed");
        // return repo.findById(id).orElse(new VendorReceivedFrom(id, queForMail));
    }

    @Transactional
    public void delete(String sysPrin, String vendorId) {
        int rows = repo.deleteOne(sysPrin, vendorId);
        if (rows == 0) {
            throw new EntityNotFoundException("No VendorReceivedFrom found for sysPrin="
                    + sysPrin + ", vendorId=" + vendorId);
        }
    }
}






package rapid.web.sysprin;

import jakarta.validation.Valid;
import lombok.RequiredArgsConstructor;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;
import rapid.model.sysprin.vendor.VendorReceivedFrom;
import rapid.service.sysprin.VendorReceivedFromService;
import rapid.web.sysprin.dto.AddVendorReceivedFromRequest;
import rapid.web.sysprin.dto.DeleteVendorReceivedFromRequest;

@RestController
@RequiredArgsConstructor
@RequestMapping("/client-sysprin-writer/api/sysprins/{sysPrin}/vendors/received-from")
public class VendorReceivedFromController {

    private final VendorReceivedFromService service;

    // POST /client-sysprin-writer/api/sysprins/{sysPrin}/vendors/received-from
    @PostMapping
    public ResponseEntity<VendorReceivedFrom> add(
            @PathVariable String sysPrin,
            @RequestBody @Valid AddVendorReceivedFromRequest req) {

        // 若 body 也带 sysPrin，则以路径为准（保持一致）
        VendorReceivedFrom created = service.add(sysPrin, req.getVendorId(), req.getQueForMail());
        return ResponseEntity.status(HttpStatus.CREATED).body(created);
    }

    // DELETE /client-sysprin-writer/api/sysprins/{sysPrin}/vendors/received-from/delete
    @DeleteMapping("/delete")
    public ResponseEntity<Void> delete(
            @PathVariable String sysPrin,
            @RequestBody @Valid DeleteVendorReceivedFromRequest req) {

        service.delete(sysPrin, req.getVendorId());
        return ResponseEntity.noContent().build();
    }
}
