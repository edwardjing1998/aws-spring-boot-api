    @Query("""
           select v.vendId           as vendId,
                  v.vendName         as vendName,
                  v.vendReceiverCode as vendReceiverCode,
                  v.fileServerName   as fileServerName,
                  v.fileServerIp     as fileServerIp
             from Vendor v
            where v.active = true
              and v.fileIo = :io
           """)
    Page<VendorTransferInformation> findActiveByFileIo(
            @Param("io") String fileIo,
            Pageable pageable
    );
