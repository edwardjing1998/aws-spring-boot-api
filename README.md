   @Transactional
    public SysPrinsPrefixDTO update(SysPrinsPrefixDTO dto) {
        List<SysPrinsPrefix>  sysPrinPrefixes = sysPrinsPrefixRepository.findByBillingSpPrefix(dto.getBillingSp(), dto.getPrefix());
        if(sysPrinPrefixes.isEmpty()){
            throw new SysPrinPrefixNotFoundException(dto.getBillingSp(), dto.getPrefix());
        }
        for(SysPrinsPrefix prefix: sysPrinPrefixes) {
            prefix.getId().setAtmCashRule(dto.getAtmCashRule());
            log.info("updating BillingSp {}, prefix {}, atmCashRule {}", prefix.getId().getBillingSp(), prefix.getId().getPrefix(), prefix.getId().getAtmCashRule());
            sysPrinsPrefixRepository.save(prefix);
            log.info("complete updating BillingSp {}, prefix {}, atmCashRule {}", prefix.getId().getBillingSp(), prefix.getId().getPrefix(), prefix.getId().getAtmCashRule());
        }
        return dto;
    }
