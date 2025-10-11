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
