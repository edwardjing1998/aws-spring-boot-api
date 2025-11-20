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
     * Old behavior: move ONE sysPrin from oldClientId -> newClientId
     * (keeping the same sysPrin code).
     */
    @Transactional
    public Optional<SysPrin> updateClient(String oldClientId, String sysPrin, String newClientId) {

        SysPrinId oldId = new SysPrinId(oldClientId, sysPrin);

        return sysPrinRepository.findById(oldId).map(oldEntity -> {
            SysPrinDTO dto = sysPrinMapper.toDto(oldEntity);
            dto.setClient(newClientId);

            SysPrin newEntity = sysPrinMapper.toEntity(dto);
            newEntity.setId(new SysPrinId(newClientId, sysPrin));

            // Save the new record only (do not delete anything here)
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

    /**
     * NEW:
     * 1) Find ONE existing SysPrin by (oldClientId, sysPrin).
     * 2) Create/save a new SysPrin with the SAME sysPrin but NEW clientId.
     * 3) Delete ALL SysPrin records that belong to oldClientId.
     * 4) Return Optional of the newly saved SysPrin.
     */
    @Transactional
    public Optional<SysPrin> transferClientAndDeleteOld(String oldClientId,
                                                        String sysPrin,
                                                        String newClientId) {
        SysPrinId oldId = new SysPrinId(oldClientId, sysPrin);

        return sysPrinRepository.findById(oldId).map(oldEntity -> {
            // ① Map old → DTO, mutate client
            SysPrinDTO dto = sysPrinMapper.toDto(oldEntity);
            dto.setClient(newClientId);

            // ② Map DTO → new entity with new composite key
            SysPrin newEntity = sysPrinMapper.toEntity(dto);
            newEntity.setId(new SysPrinId(newClientId, sysPrin));

            // ③ Save the new record first
            SysPrin saved = sysPrinRepository.save(newEntity);

            // ④ Delete ALL records for oldClientId (including the old one we just copied)
            sysPrinRepository.deleteAllByClient(oldClientId);

            // ⑤ Return the new SysPrin
            return saved;
        });
    }
}
