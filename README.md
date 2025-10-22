@Transactional
public int updateAllByClientSysPrin(SysPrinCreateRequest req) {
  return sysPrinRepository.bulkUpdateByBusinessKey(
      req.getClient(), req.getSysPrin(),
      req.getCustType(), req.getUndeliverable(),
      req.getStatA(), req.getStatB(), req.getStatC(), req.getStatD(), req.getStatE(), req.getStatF(), req.getStatI(),
      req.getStatL(), req.getStatO(), req.getStatU(), req.getStatX(), req.getStatZ(),
      req.getPoBox(), req.getAddrFlag(), req.getTempAway(), req.getRps(), req.getSession(),
      req.getBadState(), req.getAstatRch(), req.getNm13(), req.getTempAwayAtts(),
      req.getReportMethod(), req.getActive(), req.getNotes(),
      req.getReturnStatus(), req.getDestroyStatus(), req.getNonUS(), req.getSpecial(),
      req.getPinMailer(), req.getHoldDays(), req.getForwardingAddress(),
      req.getContact(), req.getPhone(), req.getEntityCode()
  );
}
