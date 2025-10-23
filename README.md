    @Modifying(clearAutomatically = true, flushAutomatically = true)
    @Transactional
    @Query(value = """
      DELETE FROM INVALID_DELIV_AREAS
      WHERE SYS_PRIN = :targetSysPrin
      """, nativeQuery = true)
    int deleteBySysPrin(String targetSysPrin);


        repo.deleteBySysPrin(targetSysPrin);
