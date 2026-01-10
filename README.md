        // ✅ receivedFrom: per sysPrin, fetch only top 10 using pageable
        // IMPORTANT: stable ordering for pagination
        PageRequest top10ByVendorId = PageRequest.of(
                0,
                10,
                Sort.by(Sort.Direction.ASC, "id.vendorId")
        );

        List<VendorReceivedFrom> receivedFromRaw = new ArrayList<>();
        for (String sp : sysPrinKeys) {
            Page<VendorReceivedFrom> page = vendorReceivedFromRepository.findByIdSysPrin(sp, top10ByVendorId);
            receivedFromRaw.addAll(page.getContent());
        }
