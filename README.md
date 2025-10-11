@Query("""
  SELECT DISTINCT c FROM Client c
  LEFT JOIN FETCH c.reportOptions ro
  WHERE c.client IN :ids
""")
List<Client> fetchReportOptionsByClientIds(Set<String> ids);

@Query("""
  SELECT DISTINCT c FROM Client c
  LEFT JOIN FETCH c.sysPrinsPrefixes spp
  WHERE c.client IN :ids
""")
List<Client> fetchSysPrinsPrefixesByClientIds(Set<String> ids);

@Query("""
  SELECT DISTINCT c FROM Client c
  LEFT JOIN FETCH c.sysPrins sp
  WHERE c.client IN :ids
""")
List<Client> fetchSysPrinsByClientIds(Set<String> ids);

@Query("""
  SELECT DISTINCT c FROM Client c
  LEFT JOIN FETCH c.clientEmails ce
  WHERE c.client IN :ids
""")
List<Client> fetchClientEmailsByClientIds(Set<String> ids);
