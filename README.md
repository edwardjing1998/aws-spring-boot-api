package rapid.repository.client;

import org.hibernate.query.Page;
import org.springframework.data.domain.Pageable;
import org.springframework.data.jpa.repository.EntityGraph;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Modifying;
import org.springframework.data.jpa.repository.Query;
import org.springframework.data.repository.query.Param;
import rapid.model.client.Client;

import java.util.List;
import java.util.Set;

public interface ClientRepository extends JpaRepository<Client, String> {

    @Query("SELECT c FROM Client c WHERE c.client IS NOT NULL AND c.client <> ''")
    List<Client> findAllValidClients();

    List<Client> findAllByClient(String client);

    @Modifying(clearAutomatically = true, flushAutomatically = true)
    @Query(value = """
        UPDATE clients
        SET
          active               = COALESCE(:#{#client.active}, active),
          addr                 = COALESCE(:#{#client.addr}, addr),
          amex_issued          = COALESCE(:#{#client.amexIssued}, amex_issued),
          billing_sp           = COALESCE(:#{#client.billingSp}, billing_sp),
          chlookup_type        = COALESCE(:#{#client.chLookUpType}, chlookup_type),
          city                 = COALESCE(:#{#client.city}, city),
          contact              = COALESCE(:#{#client.contact}, contact),
          exclude_from_report  = COALESCE(:#{#client.excludeFromReport}, exclude_from_report),
          fax_number           = COALESCE(:#{#client.faxNumber}, fax_number),
          name                 = COALESCE(:#{#client.name}, name),
          phone                = COALESCE(:#{#client.phone}, phone),
          positive_reports     = COALESCE(:#{#client.positiveReports}, positive_reports),
          report_break_flag    = COALESCE(:#{#client.reportBreakFlag}, report_break_flag),
          state                = COALESCE(:#{#client.state}, state),
          sub_client_ind       = COALESCE(:#{#client.subClientInd}, sub_client_ind),
          sub_client_xref      = COALESCE(:#{#client.subClientXref}, sub_client_xref),
          zip                  = COALESCE(:#{#client.zip}, zip)
        WHERE client = :#{#client.client}
        """, nativeQuery = true)
    int bulkUpdateByClient(@Param("client") Client client);

    @Query("""
    SELECT c
    FROM Client c
    WHERE c.client = :clientId
    """)
    List<Client> findAllRawByClientId(@Param("clientId") String clientId);

    @Modifying(clearAutomatically = true, flushAutomatically = true)
    @Query("delete from Client c where c.client = :clientId")
    void hardDeleteByClient(@Param("clientId") String clientId);

    @EntityGraph(attributePaths = {
            "reportOptions",
            "sysPrinsPrefixes",
            "sysPrins",
            "clientEmails"
    })
    @Query("select c from Client c")
    List<Client> findPageWithChildren(Pageable pageable);

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
        List<Client> findPage(Pageable pageable);


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

}
