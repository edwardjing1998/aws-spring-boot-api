@Query("""
select v
from VendorReceivedFrom v
where v.id.sysPrin in :sysPrins
and row_number() over(partition by v.id.sysPrin order by v.id.vendorId) <= :limit
""")
List<VendorReceivedFrom> findTopNPerSysPrin(@Param("sysPrins") Collection<String> sysPrins,
                                            @Param("limit") int limit);
