    // HQL/JPQL delete a single row by composite key
    @Modifying
    @Transactional
    @Query("""
           delete from VendorReceivedFrom v
           where v.id.sysPrin = :sysPrin and v.id.vendorId = :vendorId
           """)
    int deleteOne(@Param("sysPrin") String sysPrin, @Param("vendorId") String vendorId);

    // (optional) delete all for a sysPrin
    @Modifying
    @Transactional
    @Query("""
           delete from VendorReceivedFrom v
           where v.id.sysPrin = :sysPrin
           """)
    int deleteBySysPrin(@Param("sysPrin") String sysPrin);

    // JPQL/HQL can't do INSERT. Provide a convenience "add" that uses save(...)
    default VendorReceivedFrom addOne(String sysPrin, String vendorId, Boolean queForMail) {
        var id = new VendorSentToId(vendorId, sysPrin);
        var entity = new VendorReceivedFrom(id, queForMail);
        return save(entity);
    }
