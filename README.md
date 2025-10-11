Page<Client> page = repo.findPage(pageable);
if (!page.isEmpty()) {
  Set<String> ids = page.map(Client::getClient).toSet();
  repo.fetchReportOptionsByClientIds(ids);
  repo.fetchSysPrinsPrefixesByClientIds(ids);
  repo.fetchSysPrinsByClientIds(ids);
  repo.fetchClientEmailsByClientIds(ids);
}
return page;
