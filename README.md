@Query(
  value = """
    select c
    from Client c
    where c.client is not null and c.client <> '' and c.name <> ''
    """,
  countQuery = """
    select count(c)
    from Client c
    where c.client is not null and c.client <> '' and c.name <> ''
    """
)
Page<Client> findPage(Pageable pageable);





Pageable pageable = PageRequest.of(page, size, Sort.by("client").ascending());
Page<Client> p = repo.findPage(pageable);
