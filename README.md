public interface SysPrinRepository extends JpaRepository<SysPrin, SysPrinId> {
  @Modifying(clearAutomatically = true, flushAutomatically = true)
  @Query("delete from SysPrin sp where sp.id.client = :clientId")
  int hardDeleteByClient(@Param("clientId") String clientId);
}

public interface ClientEmailRepository extends JpaRepository<ClientEmail, ClientEmailId> {
  @Modifying(clearAutomatically = true, flushAutomatically = true)
  @Query("delete from ClientEmail ce where ce.id.clientId = :clientId")
  int hardDeleteByClient(@Param("clientId") String clientId);
}

public interface ClientReportOptionRepository extends JpaRepository<ClientReportOption, ClientReportOptionId> {
  @Modifying(clearAutomatically = true, flushAutomatically = true)
  @Query("delete from ClientReportOption cro where cro.id.clientId = :clientId")
  int hardDeleteByClient(@Param("clientId") String clientId);
}

// NOTE: your mapping ties prefixes to billingSp, not clientId.
public interface SysPrinsPrefixRepository extends JpaRepository<SysPrinsPrefix, SysPrinsPrefixId> {
  @Modifying(clearAutomatically = true, flushAutomatically = true)
  @Query("delete from SysPrinsPrefix spp where spp.id.billingSp = :billingSp")
  int hardDeleteByBillingSp(@Param("billingSp") String billingSp);
}

















@Service
@RequiredArgsConstructor
public class ClientService {
  private final ClientRepository clientRepo;
  private final SysPrinRepository sysPrinRepo;
  private final ClientEmailRepository emailRepo;
  private final ClientReportOptionRepository reportRepo;
  private final SysPrinsPrefixRepository prefixRepo;

  @Transactional
  public void deleteClientDeep(String clientId) {
    Client client = clientRepo.findById(clientId)
        .orElseThrow(() -> new IllegalArgumentException("Client not found: " + clientId));

    // 1) bulk-delete children (no hydration => no duplicate-id errors)
    emailRepo.hardDeleteByClient(clientId);
    reportRepo.hardDeleteByClient(clientId);
    sysPrinRepo.hardDeleteByClient(clientId);
    if (client.getBillingSp() != null) {
      prefixRepo.hardDeleteByBillingSp(client.getBillingSp());
    }

    // 2) delete the parent
    clientRepo.deleteById(clientId);
  }
}







