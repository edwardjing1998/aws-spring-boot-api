package rapid.service.sysprin;

import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;
import rapid.dto.sysPrin.SysPrinDTO;
import rapid.model.sysprin.SysPrin;
import rapid.model.sysprin.key.SysPrinId;
import rapid.repository.sysprin.SysPrinRepository;
import rapid.sysPrin.mapper.SysPrinMapper;

import java.util.List;
import java.util.Optional;

@Service
@RequiredArgsConstructor
public class SysPrinClientTransferService {

    private final SysPrinRepository sysPrinRepository;
    private final SysPrinMapper sysPrinMapper;

    /**
     * Try to update the client of a SysPrin.
     * Returns Optional.empty() if the old SysPrin does not exist.
     */
    @Transactional
    public Optional<SysPrin> updateClient(String oldClientId, String sysPrin, String newClientId) {

        List<SysPrin> oldEntities = sysPrinRepository.findByIdClient(oldClientId);

        return oldEntities.stream().findFirst().map(oldEntity -> {
            SysPrinDTO dto = sysPrinMapper.toDto(oldEntity);
            dto.setClient(newClientId);

            SysPrin newEntity = sysPrinMapper.toEntity(dto);
            newEntity.setId(new SysPrinId(newClientId, sysPrin));
            return sysPrinRepository.save(newEntity);
        });
    }

    public List<SysPrin> findAllByIdClient(String clientId){
        return sysPrinRepository.findByIdClient(clientId);
    }

    @Transactional
    public int deleteAllForClientAndSysPrin(String client, String sysPrin) {
        return sysPrinRepository.deleteByClientAndSysPrin(client, sysPrin);
    }
}
