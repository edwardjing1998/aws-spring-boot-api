import org.hibernate.Hibernate;
import org.springframework.data.domain.PageRequest;
import org.springframework.data.domain.Sort;
import org.springframework.transaction.annotation.Transactional;

@Transactional(readOnly = true)
public List<Client> getClientsWithChildren(int page, int size) {
  var pageable = PageRequest.of(page, size, Sort.by("client").ascending());
  var pageResult = repo.findPage(pageable);

  var roots = pageResult.getContent();
  if (roots.isEmpty()) return roots;

  var ids = roots.stream().map(Client::getClient).collect(java.util.stream.Collectors.toSet());

  // hydrate children (one collection per query to avoid MultipleBagFetchException)
  repo.fetchReportOptionsByClientIds(ids);
  repo.fetchSysPrinsPrefixesByClientIds(ids);
  repo.fetchSysPrinsByClientIds(ids);
  repo.fetchClientEmailsByClientIds(ids);

  // ensure initialized before returning (safe if controller runs outside tx)
  for (var c : roots) {
    Hibernate.initialize(c.getReportOptions());
    Hibernate.initialize(c.getSysPrinsPrefixes());
    Hibernate.initialize(c.getSysPrins());
    Hibernate.initialize(c.getClientEmails());
  }
  return roots;
}
