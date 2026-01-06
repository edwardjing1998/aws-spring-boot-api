import org.springframework.data.domain.Page;
import org.springframework.data.domain.PageRequest;
import org.springframework.data.domain.Sort;

Page<VendorTransferInformation> page =
        vendorRepository.findActiveByFileIo(
                "O",
                PageRequest.of(
                        0,                  // page index (0-based)
                        20,                 // page size
                        Sort.by("vendId")   // ⚠️ 推荐显式排序
                )
        );
