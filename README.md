// ClientRepository
@Query(
  value = """
    SELECT c FROM Client c
    WHERE c.client IS NOT NULL AND c.client <> '' AND c.name <> ''
    ORDER BY c.client
  """,
  countQuery = """
    SELECT COUNT(c) FROM Client c
    WHERE c.client IS NOT NULL AND c.client <> '' AND c.name <> ''
  """
)
Page<Client> findPage(Pageable pageable);







// ClientRepository (add these)
@Query("""
  SELECT DISTINCT c FROM Client c
  LEFT JOIN FETCH c.reportOptions ro
  WHERE c.client IN :ids
""")
List<Client> fetchReportOptionsByClientIds(@org.springframework.data.repository.query.Param("ids") java.util.Set<String> ids);

@Query("""
  SELECT DISTINCT c FROM Client c
  LEFT JOIN FETCH c.sysPrinsPrefixes spp
  WHERE c.client IN :ids
""")
List<Client> fetchSysPrinsPrefixesByClientIds(@org.springframework.data.repository.query.Param("ids") java.util.Set<String> ids);

@Query("""
  SELECT DISTINCT c FROM Client c
  LEFT JOIN FETCH c.sysPrins sp
  WHERE c.client IN :ids
""")
List<Client> fetchSysPrinsByClientIds(@org.springframework.data.repository.query.Param("ids") java.util.Set<String> ids);

@Query("""
  SELECT DISTINCT c FROM Client c
  LEFT JOIN FETCH c.clientEmails ce
  WHERE c.client IN :ids
""")
List<Client> fetchClientEmailsByClientIds(@org.springframework.data.repository.query.Param("ids") java.util.Set<String> ids);
