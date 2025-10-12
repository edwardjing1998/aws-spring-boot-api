package rapid.service.client;

import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Service;
import rapid.client.mapper.ClientEmailMapper;
import rapid.dto.client.ClientEmailDTO;
import rapid.model.client.Client;
import rapid.model.client.ClientEmail;
import rapid.repository.client.ClientEmailRepository;
import rapid.repository.client.ClientRepository;

import java.util.*;


@Service
@RequiredArgsConstructor
@Slf4j
public class ClientEmailService {

    private final ClientRepository clientRepository;
    private final ClientEmailRepository clientEmailRepository;
    private final ClientEmailMapper clientEmailMapper;


    /** Returns empty if clientId doesn't exist (no exceptions thrown). */
    public Optional<ClientEmailDTO> saveClientEmailFromDTO(ClientEmailDTO dto) {
            log.info("the client id {} received from email added request ", dto.getClientId());
        if (clientRepository.findAllByClient(dto.getClientId()).isEmpty()) {
            log.info("the client id {} can not be found ", dto.getClientId());
            return Optional.empty();
        }
        ClientEmail email = clientEmailMapper.toEntity(dto);
        log.info("client {} email {} added request ", dto.getClientId(), email.getEmailNameTx());
        return Optional.of(clientEmailMapper.toDTO(clientEmailRepository.save(email)));
    }

    public void deleteClientEmailById(String clientId, String emailAddress) {
                 clientEmailRepository
                .findByIdClientIdAndIdEmailAddressTx(clientId, emailAddress)
                .ifPresent(clientEmailRepository::delete);
    }


    public boolean clientEmailExists(String clientId, String emailAddress) {
        return   clientEmailRepository
                .findByIdClientIdAndIdEmailAddressTx(clientId, emailAddress)
                .isPresent();
    }

        public List<ClientEmailDTO> findEmailsByClientId(String clientId) {
        return clientEmailRepository
                .findByIdClientId(clientId)
                .stream()
                .map(clientEmailMapper::toDTO)
                .toList();
    }

    public boolean clientExists(String clientId) {
        return clientRepository.existsById(clientId);
    }

    public List<Client> clients(String clientId) {
        return clientRepository.findAllRawByClientId(clientId);
    }

}
