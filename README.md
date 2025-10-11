package rapid.service.client;

import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.hibernate.Hibernate;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.PageRequest;
import org.springframework.data.domain.Pageable;
import org.springframework.data.domain.Sort;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;
import rapid.client.mapper.ClientMapper;
import rapid.client.mapper.SysPrinsPrefixMapper;
import rapid.dto.client.ClientDTO;
import rapid.dto.client.SysPrinsPrefixDTO;
import rapid.exception.client.ClientNotFoundException;
import rapid.model.client.Client;
import rapid.model.client.SysPrinsPrefix;
import rapid.model.client.key.SysPrinsPrefixId;
import rapid.repository.client.ClientEmailRepository;
import rapid.repository.client.ClientReportOptionRepository;
import rapid.repository.client.ClientRepository;
import rapid.repository.sysprin.SysPrinRepository;
import rapid.repository.sysprin.SysPrinsPrefixRepository;

import java.util.*;
import java.util.stream.Collectors;

@Service
@RequiredArgsConstructor
@Slf4j
public class ClientService {

    private final ClientRepository clientRepository;
    private final SysPrinRepository sysPrinRepository;
    private final ClientEmailRepository emailRepository;
    private final ClientReportOptionRepository reportRepository;
    private final SysPrinsPrefixRepository sysPrinsPrefixRepository;

    private final ClientMapper clientMapper;
    private final SysPrinsPrefixMapper prefixMapper;

    public ClientDTO saveClient(ClientDTO dto) {
        Client entity = clientMapper.toEntity(dto);
        return clientMapper.toDTO(clientRepository.save(entity));
    }

    /** Returns false if prefix doesn't exist; true if deleted (no exceptions thrown). */
    @Transactional
    public boolean deleteByBillingSpPrefixAndAtmCashRule(String billingSp, String prefix, String atmCashRule) {
        SysPrinsPrefixId id = new SysPrinsPrefixId(billingSp, prefix, atmCashRule);
        if (!sysPrinsPrefixRepository.existsById(id)) {
            return false;
        }
        sysPrinsPrefixRepository.deleteById(id);
        return true;
    }

    public SysPrinsPrefixDTO save(SysPrinsPrefixDTO dto) {
        SysPrinsPrefix entity = prefixMapper.toEntity(dto);
        SysPrinsPrefix saved = sysPrinsPrefixRepository.save(entity);
        return prefixMapper.toDto(saved);
    }

    /** Returns empty if client doesn't exist (no exceptions thrown). */
    @Transactional
    public List<ClientDTO> updateClient(ClientDTO dto) {

        // Map DTO -> Client entity for SpEL query
        Client param = clientMapper.toEntity(dto);

        // Bulk update ALL rows matching client
        int updated = clientRepository.bulkUpdateByClient(param);
        if (updated == 0) {
            return List.of(); // nothing updated
        }

        // Read back ALL rows with this client
        List<Client> updatedRows = clientRepository.findAllByClient(dto.getClient());

        // Map each to DTO
        return updatedRows.stream()
                .map(clientMapper::toDTO)
                .toList();
    }

    public List<ClientDTO> getAllClientsById(String clientId){

        List<Client> clients = clientRepository.findAllByClient(clientId);

        return clients.stream()
                .map(clientMapper::toDTO)
                .toList();
    }


    // Update active status for all clients with the given clientId
    /*  1. Fetch all clients matching the provided clientId.
        2. If no clients are found, log the event and throw ClientNotFoundException.
        3. If clients are found, iterate through each client and update its active status.
        4. Save all updated clients back to the repository.
        5. Log the successful update and return a confirmation message.
     */
    @Transactional
    public String updateClientActiveStatus(String clientId, Boolean activeStatus) {
        List<Client> clients = clientRepository.findAllByClient(clientId);
        if (clients.isEmpty()) {
            log.info("Client ID : {} not found to update the active status.", clientId);
            throw new ClientNotFoundException(clientId);
        }

        log.info("clients found to update the active status : {}", clients.size());
        // Update active status for all matching clients
        for (Client client : clients) {
            client.setActive(activeStatus);
        }
        log.info("Updated active status for client ID: {} to {}", clientId, activeStatus);
        clientRepository.saveAll(clients);

       return "Client ID: " + clientId + " active status updated to " + activeStatus;
    }

    @Transactional
    public String deleteClientDeep(String clientId) {
        List<Client> clients = clientRepository.findAllByClient(clientId);

        if (clients.isEmpty()) {
            log.info("Client ID : {} not found to update the active status.", clientId);
            throw new ClientNotFoundException(clientId);
        }


        // 1) bulk-delete children (no hydration => no duplicate-id errors)
        emailRepository.hardDeleteByClient(clientId);
        reportRepository.hardDeleteByClient(clientId);
        sysPrinRepository.hardDeleteByClient(clientId);
        if (clients.getFirst().getBillingSp() != null) {
            sysPrinsPrefixRepository.hardDeleteByBillingSp(clients.getFirst().getBillingSp());
        }

        // 2) delete the parent
        clientRepository.hardDeleteByClient(clientId);

       return "Client details deleted successfully for ID: " + clientId;
    }

    public List<Client> clientIsExist(String clientId){
        return clientRepository.findAllByClient(clientId);
    }

    /*public Page<Client> getClientsWithChildren(int page, int size) {
        Pageable pageable = PageRequest.of(page, size);
        Page<Client> clients = clientRepository.findPage(pageable);
        log.info("the client records {} found in page {} and size {} ", clients.getSize(), page, size);
        if (!clients.isEmpty()) {
            Set<String> ids = clients.stream()
                    .map(Client::getClient)
                    .collect(Collectors.toSet());
            clientRepository.fetchReportOptionsByClientIds(ids);
            clientRepository.fetchSysPrinsPrefixesByClientIds(ids);
            clientRepository.fetchSysPrinsByClientIds(ids);
            clientRepository.fetchClientEmailsByClientIds(ids);
        }
        return clients;
    }*/

    @Transactional(readOnly = true)
    public List<Client> getClientsWithChildren(int page, int size) {
        Pageable pageable = PageRequest.of(page, size, Sort.by("client").ascending());
        Page<Client> pageResult = clientRepository.findPage(pageable);

        var roots = pageResult.getContent();
        if (roots.isEmpty()) return roots;

        var ids = roots.stream().map(Client::getClient).collect(java.util.stream.Collectors.toSet());

        // hydrate children (one collection per query to avoid MultipleBagFetchException)
        clientRepository.fetchReportOptionsByClientIds(ids);
        clientRepository.fetchSysPrinsPrefixesByClientIds(ids);
        clientRepository.fetchSysPrinsByClientIds(ids);
        clientRepository.fetchClientEmailsByClientIds(ids);

        // ensure initialized before returning (safe if controller runs outside tx)
        for (var c : roots) {
            Hibernate.initialize(c.getReportOptions());
            Hibernate.initialize(c.getSysPrinsPrefixes());
            Hibernate.initialize(c.getSysPrins());
            Hibernate.initialize(c.getClientEmails());
        }
        return roots;
    }


}



package rapid.repository.client;

import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;
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


        @Query("""
      SELECT DISTINCT c FROM Client c
      LEFT JOIN FETCH c.reportOptions ro
      WHERE c.client IN :ids
    """)
        void fetchReportOptionsByClientIds(Set<String> ids);

    @Query("""
          SELECT DISTINCT c FROM Client c
          LEFT JOIN FETCH c.sysPrinsPrefixes spp
          WHERE c.client IN :ids
        """)
    void fetchSysPrinsPrefixesByClientIds(Set<String> ids);

            @Query("""
          SELECT DISTINCT c FROM Client c
          LEFT JOIN FETCH c.sysPrins sp
          WHERE c.client IN :ids
        """)
            void fetchSysPrinsByClientIds(Set<String> ids);

            @Query("""
          SELECT DISTINCT c FROM Client c
          LEFT JOIN FETCH c.clientEmails ce
          WHERE c.client IN :ids
        """)
            void fetchClientEmailsByClientIds(Set<String> ids);

}
