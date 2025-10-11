public interface ClientRepository extends JpaRepository<Client, String> {

  @EntityGraph(attributePaths = {
      "reportOptions",
      "sysPrinsPrefixes",
      "sysPrins",
      "clientEmails"
  })
  @Query("select c from Client c")
  Page<Client> findPageWithChildren(Pageable pageable);
}





@Service
public class ClientService {
  private final ClientRepository repo;

  public ClientService(ClientRepository repo) { this.repo = repo; }

  public Page<Client> getClientsWithChildren(int page, int size) {
    var pageable = PageRequest.of(page, size, Sort.by("client"));
    return repo.findPageWithChildren(pageable);
  }
}
