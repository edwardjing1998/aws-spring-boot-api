// import org.springframework.data.domain.Page;
// import org.springframework.data.domain.PageImpl;
// import org.springframework.data.domain.PageRequest;
// import org.springframework.data.domain.Pageable;

@GetMapping("/report-option")
public ResponseEntity<Page<ClientReportOptionDTO>> getAllReports(
        @RequestParam(defaultValue = "0") int page,
        @RequestParam(defaultValue = "20") int size) {

    Pageable pageable = PageRequest.of(page, size);
    Page<ClientReportOptionDTO> dtoPage = service.getAllWithDetails(pageable);

    return ResponseEntity.ok(dtoPage);
}
