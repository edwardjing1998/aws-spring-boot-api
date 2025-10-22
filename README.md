    @Transactional
    public SysPrinDTO update(SysPrinCreateRequest req) {
        // Assumes controller has already validated inputs & conflicts
        SysPrinId id = new SysPrinId(req.getClient(), req.getSysPrin());

        SysPrin entity = sysPrinMapper.fromCreateRequest(req);
        entity.setId(id);

        SysPrin saved = sysPrindataFetcher.getSysPrinRepository().save(entity);
        return sysPrinMapper.toDto(saved);
    }
