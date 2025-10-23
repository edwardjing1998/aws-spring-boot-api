    /** Delete all rows for the target sys_prin */
    @Modifying(clearAutomatically = true, flushAutomatically = true)
    @Transactional
    @Query(value = """
        DELETE FROM VENDOR_SENT_TO
        WHERE SYS_PRIN = :targetSysPrin
        """, nativeQuery = true)
    int deleteBySysPrin(String targetSysPrin);

    /** Copy rows from template -> target */
    @Modifying(clearAutomatically = true, flushAutomatically = true)
    @Transactional
    @Query(value = """
        INSERT INTO VENDOR_SENT_TO (VEND_ID, SYS_PRIN, QUEFORMAIL_CD)
        SELECT src.VEND_ID,
               :targetSysPrin,
               src.QUEFORMAIL_CD
        FROM VENDOR_SENT_TO src
        WHERE src.SYS_PRIN = :templateSysPrin
        """, nativeQuery = true)
    int copyFromTemplate(String templateSysPrin, String targetSysPrin);
