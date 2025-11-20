    @Transactional
    public Optional<SysPrin> updateClient(String oldClientId, String sysPrin, String newClientId) {
        SysPrinId oldId = new SysPrinId(oldClientId, sysPrin);

        return sysPrinRepository.findById(oldId).map(oldEntity -> {
            SysPrinDTO dto = sysPrinMapper.toDto(oldEntity);
            dto.setClient(newClientId);

            SysPrin newEntity = sysPrinMapper.toEntity(dto);
            newEntity.setId(new SysPrinId(newClientId, sysPrin));

            sysPrinRepository.delete(oldEntity);
            return sysPrinRepository.save(newEntity);
        });
    }
