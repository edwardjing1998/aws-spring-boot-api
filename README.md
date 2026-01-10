@Query(
  value = """
    select vrf
    from VendorReceivedFrom vrf
    join fetch vrf.vendor v
    where vrf.id.sysPrin = :sysPrin and v.fileIo = :fileIo
  """,
  countQuery = """
    select count(vrf)
    from VendorReceivedFrom vrf
    join vrf.vendor v
    where vrf.id.sysPrin = :sysPrin and v.fileIo = :fileIo
  """
)
Page<VendorReceivedFrom> findPageWithVendorBySysPrinAndFileIo(
    @Param("sysPrin") String sysPrin,
    @Param("fileIo") String fileIo,
    Pageable pageable
);
