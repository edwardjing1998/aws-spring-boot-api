    public Page<VendorReceivedFromDTO> getReceivedFromDtosBySysPrin(String sysPrin, Pageable pageable) {
        // repo returns Page<VendorReceivedFrom>
        var page = repository.findByIdSysPrin(sysPrin, pageable);

        // ✅ keep pagination metadata, map each row to DTO
        return page.map(mapper::toDto);
    }
