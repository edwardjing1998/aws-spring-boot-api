public interface SysPrinRepository extends JpaRepository<SysPrin, SysPrinId> {

    @Modifying
    @Transactional
    @Query("DELETE FROM SysPrin s WHERE s.id.client = :client AND s.id.sysPrin = :sysPrin")
    int deleteByClientAndSysPrin(@Param("client") String client,
                                 @Param("sysPrin") String sysPrin);
}
