@Transactional
public Optional<SysPrin> updateClient(String oldClientId, String sysPrin, String newClientId) {
    SysPrinId oldId = new SysPrinId(oldClientId, sysPrin);

    // now returns List<SysPrin>
    List<SysPrin> oldEntities = sysPrinRepository.findByIdClient(oldId);

    // take ONE record (the first) and keep Optional<SysPrin> behavior
    return oldEntities.stream().findFirst().map(oldEntity -> {
        SysPrinDTO dto = sysPrinMapper.toDto(oldEntity);
        dto.setClient(newClientId);

        SysPrin newEntity = sysPrinMapper.toEntity(dto);
        newEntity.setId(new SysPrinId(newClientId, sysPrin));

        sysPrinRepository.delete(oldEntity);
        return sysPrinRepository.save(newEntity);
    });
}
