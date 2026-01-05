public interface ClientEmailRepository extends JpaRepository<ClientEmail, ClientEmailId> {
    List<ClientEmail> findByIdClientId(String clientId);
    List<ClientEmail> findByIdClientId(String clientId, Pageable pageable);


    List<ClientEmail> findByIdClientIdIn(List<String> clientIds);
    Optional<ClientEmail> findByIdClientIdAndIdEmailAddressTx(String clientId, String emailAddressTx);

    @Modifying(clearAutomatically = true, flushAutomatically = true)
    @Query("delete from ClientEmail ce where ce.id.clientId = :clientId")
    void hardDeleteByClient(@Param("clientId") String clientId);
}
